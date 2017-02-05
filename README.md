# Initialize a Rails application using Nix

## Motivation

Recently, I needed to create a new Rails application, and now that I'm
using [NixOS](https://nixos.org/) and [Nix](https://nixos.org/nix/), I had no
idea about how to do it. Fortunately, Ruby development using Nix seemed solved,
so I just needed a way to initialize a new Rails application with Nix and using
its [development environments](http://nixos.org/nix/manual/#name-2).

This template will help you with the process, generating a new project with the
latest version of Rails (5.0.1).

## Usage

First of all, clone the project:

```
$ git clone https://github.com/areina/nix-new-rails-app.git my_new_rails_app
```

### Automatic

There is a script `script/init` that executes all the necessary steps to have
your new shiny Rails application initialized. I will describe all the steps
below, and you can execute them manually if you prefer.

### Manual

1. Generate a `Gemfile.lock` with all necessary gems for creating a Rails app. 

    ```
    $ nix-shell -p bundler --command "bundler lock"
    ```

2. Generate a `gemset.nix` with nix packages definitions for those gems. 
    ```
    $ $(nix-build '<nixpkgs>' -A bundix)/bin/bundix
    ```

3. Initialize the rails app and generate a new `Gemfile.lock` with dependencies
for this new app.
    ```
    $ nix-shell --command "rails new . --force --skip-bundle; bundler lock"
    ```

4. Generate a new `gemset.nix` with all package dependencies for running your app
    ```
    $ $(nix-build '<nixpkgs>' -A bundix)/bin/bundix
    ```

At this point, your app is initialized. You can enter to your isolated
development environment and run for example the rails server:

```
$ nix-shell
[nix-shell:...] $ rails server
=> Booting Puma
=> Rails 5.0.1 application starting in development on http://localhost:3000
=> Run `rails server -h` for more startup options
Puma starting in single mode...
* Version 3.7.0 (ruby 2.3.3-p222), codename: Snowy Sagebrush
* Min threads: 5, max threads: 5
* Environment: development
* Listening on tcp://0.0.0.0:3000
Use Ctrl-C to stop
```

Visit `http://localhost:3000` and you should see the Rails default page and
in your shell something like this, confirming that we're using nix packages:
```
Started GET "/" for 127.0.0.1 at 2017-02-05 22:14:25 +0100
Processing by Rails::WelcomeController#index as HTML
  Parameters: {"internal"=>true}
  Rendering /nix/store/x5idkhdyk38i50fs4n6hbhv07ipm3vbz-rb/lib/ruby/gems/2.3.0/gems/railties-5.0.1/lib/rails/templates/rails/welcome/index.html.erb
  Rendered /nix/store/x5idkhdyk38i50fs4n6hbhv07ipm3vbz-rb/lib/ruby/gems/2.3.0/gems/railties-5.0.1/lib/rails/templates/rails/welcome/index.html.erb (3.5ms)
Completed 200 OK in 14ms (Views: 6.4ms | ActiveRecord: 0.0ms)
```

Now that you're ready for hacking your new Rails app, remember removing the
`.git` directory to be detached of this repo, and start tracking your own
changes.  You can execute `script/eject` or do it manually:

```
$ rm -rf .git/
$ rm -rf script/
# And probably you will like to init your own history :-)
$ git init
```

