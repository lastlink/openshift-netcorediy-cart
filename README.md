OpenShift NetCore Cartridge
======================
# in development, basing it off of go cartridge that was based off of heroku

Runs [NetCore](http://url) on [OpenShift](https://openshift.redhat.com/app/login) using downloadable cartridge support.  To install to OpenShift from the CLI (you'll need version 1.9 or later of rhc), run:

    rhc create-app mycart http://cdk-claytondev.rhcloud.com --from-code=git://github.com/url.git

Once the app is created, you'll need to create and add a "" file in your repo to tell the cartridge what the package of your NetCore code is.  A typical 'file' file might contain:

    github.com/url/goexample

which would tell OpenShift to place all of the files in the root of the Git repository inside of the <code>github.com/url/goexample</code> package prior to compilation.

When you push code to the repo, the cart will compile your package into <code>$OPENSHIFT_REPO_DIR/bin/</code>, with the last segment of the .godir being the name of the executable.  For the above .godir, your executable will be:

    $OPENSHIFT_REPO_DIR/bin/goexample

If you want to serve web requests (vs. running in the background), you'll need to listen on the ip address and port that OpenShift allocates - those are available as OPENSHIFT_GO_IP and OPENSHIFT_GO_PORT in the environment.

The repository contains a sample net file which will print "hello, world" when someone hits your web application - see [netcore.net](https://github.com/url/openshift-netcore-cart/blob/master/template/netcore).

Any log output will be generated to <code>$OPENSHIFT_GO_LOG_DIR</code>.

To provide your own custom NETPATH directory, add a "FILE" file to the root of your Git repo with the desired GOPATH location, *relative to the $OPENSHIFT_HOMEDIR directory*. Example of a .NETpath file content: `app-root/data/mygopath`. The specified location will be additive to the existing GOPATH provided by the cartridge as the *first path* declared in GOPATH (notice the [NETCORE spec](http://URL_environment_variable) defines that "Go searches each directory listed in GOPATH to find source code, but new packages are always downloaded into the first directory in the list"). 


How it Works
------------

When you push code to your repo, a Git postreceive hook runs and invokes the bin/compile script.  This attempts to download a NetCore environment for you into $OPENSHIFT_GO_DIR/cache.  Once the environment is setup, the cart runs

    go get -tags openshift ./...

on a working copy of your source.  The main file that you run will have access to two environment variables, $OPENSHIFT_GO_IP and $OPENSHIFT_GO_PORT, which contain the internal address you must listen on to receive HTTP requests to your application.


Credits
-------

The bin/compile script is based on the [Heroku .NET Core buildpack](https://github.com/ORuban/dotnet-core-buildpack), adapted for OpenShift cartridges.


<!--
# dotnet-core-buildpack
Experimental Heroku Buildpack for .NET Core project

----
## Examples
Use below button to install [fork](https://github.com/ORuban/aspnetcore-angular2-universal)
of [ASP.NET Core & Angular 2+ Universal starter](https://github.com/MarkPieszak/aspnetcore-angular2-universal)

<a href="https://dashboard.heroku.com/new?template=https://github.com/ORuban/aspnetcore-angular2-universal.git">
  <img src="https://www.herokucdn.com/deploy/button.svg" alt="Deploy">
</a>

Life link: https://aspnetcore-angular2-universal.herokuapp.com


It uses the following `app.json` to set up app on Heroku:

```json
{
  "name": "aspnetcore-angular2-universal",
  "description": "Deploy Angular 2+ Universal & ASP.NET Core SPA Advanced Starter on Heroku",
  "logo": "https://raw.githubusercontent.com/herokumx/herokumxnet/master/NETChatterGroup.png",
  "keywords": ["heroku", "asp.net-core", "angular2", "spa"],
  "env": {
    "NPM_CONFIG_PRODUCTION": {
      "description": "False as we need to install devDependencies on heroku instance (webpack, ...)",
      "value" : "false"
    }
  },
  "buildpacks": [
    {
      "url": "heroku/nodejs"
    },
    {
      "url": "https://github.com/ORuban/dotnet-core-buildpack.git"
    }
  ]
}
```

## Useful links
- [HEROKU app.json Schema](https://devcenter.heroku.com/articles/app-json-schema)
- [Creating a 'Deploy to Heroku' Button](https://devcenter.heroku.com/articles/heroku-button)-->
