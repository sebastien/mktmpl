```
                     __              __                       ___      
                    /\ \            /\ \__                   /\_ \     
   ___ ___      __  \ \ \/'\      __\ \ ,_\   ___ ___   _____\//\ \    
 /' __` __`\  /'__`\ \ \ , <    /'__`\ \ \/ /' __` __`\/\ '__`\\ \ \   
 /\ \/\ \/\ \/\ \L\.\_\ \ \\`\ /\  __/\ \ \_/\ \/\ \/\ \ \ \L\ \\_\ \_ 
 \ \_\ \_\ \_\ \__/.\_\\ \_\ \_\ \____\\ \__\ \_\ \_\ \_\ \ ,__//\____\
  \/_/\/_/\/_/\/__/\/_/ \/_/\/_/\/____/ \/__/\/_/\/_/\/_/\ \ \/ \/____/
                                                          \ \_\        
```

# `maketmpl` ― Makefile-based templates

*Maketmpl* is a self-contained `Makefile` that generates project files
given a template. Compared to simliar tools, *maketmpl* does not require
to install any package or tool besides what you'd find a regular Unix
development environment.

## Quickstart

### Creating a template

Clone the `maketmpl` repository

```
$ git clone git@github.com:sebastien/maketmpl.git YOUR_NEW_TEMPLATE
$ cd YOUR_NEW_TEMPLATE
```  

Populate your template files and directories. Any file or directory containing
uppercase letters surrounded by brackets, like `{VARIABLE}` will be automatically
identified as **template variables**. Likewise, any file ending in `.mktmpl` will
has occurences of `{VARIABLE}` replaced by the actual value of the variable
as defined by the user of the template. The `.mktmpl` suffix will also be dropped
when applying the template.

``` 
$ mkdir tmpl
$ echo "# {PROJECT} Readme" > tmpl/README.mktmpl
$ mkdir 'tmpl/src/{LANG}/{PROJECT}'
$ echo "# This is the main file for {PROJECT}" > 'tmpl/src/{LANG}/{PROJECT}/main.{LANG}'
$ vi README.md
```

At any point you can test your template by running `make apply`. This will
ask you to edit a configuration file using `$EDITOR` (or `vi`) and will
populate `.dist` with the applied `tmpl` files and directories.

Once you're done, you simply need to add the `tmpl` files to the repository
and publish it!

### Using a template

If you'd like to create a new project from a *maketmpl* template, you
would simply need to do the following:

```
$ git clone YOUR_TEMPLATE_REPOSITORY YOUR_NEW_PROJECT
$ cd YOUR_NEW_PROJECT
$ make
```

The first run of `make` will pop-up your `$EDITOR` and ask you to fill-in the
main variables for the project. If everything worked fine, the original makefile
will be removed and the templates will be expanded.

The original template files will be moved to the `.tmpl` directory within your project. If 
you'd like to re-generate the project, simply `pushd .tmpl ; make revert ; popd` and
then either `make config` to change configuration options or `make` to rebuild
everything.

## Makefile rules 

- `make all` ― The default rule that does `make apply` followed by `make cleanup`

- `make manifest`, `make mf` ― Lists the files that are part of the project template. 

- `make variables`, `make vars` ― Lists the variables defined 

- `make configure`, `make config` ― Creates the `Makefile.conf` (or `$TEMPLATE_CONF`) file and runs
  `$EDITOR` on it. Once the editor quits, the makefile will try to 
   apply the configuration to the templates.

- `make apply` ­― Applies the configuration to produce the templates in the
  `$PRODUCT_PATH?=.dist` directory. All the files in
  `tmpl` will be expanded using the configuration and written
  to `.dist`.

- `make cleanup` ― Cleans up the *maketmpl* files and moves all the `.dist`
  files within the current directory. The *maketmpl* files
  are then backed up to `.tmpl`. You can revert everything
  by `cd .tmpl ; make revert`.

- `make revert` ―  Reverts a `make cleanup`, returning to the result
   of `make apply` in the parent directory.

- `make rules` ― Lists the generated makefile rules  that produce the applied
   template files. This is mostly useful for debugging.

## Makefile variables

- `TEMPLATE_OUTPUT`: The path where the template should be output. By default, this is
  the folder in which the template makefile is located.

- `TEMPLATE_CONF`: The name of the makefile template configuration file where the 
  template variable's values is going to be stored.

- `TEMPLATE_PATH`: The path where the template sources/files are located. By default,
  it is `tmpl`

- `TEMPLATE_BACKUP`: The path where the original template directory will be moved once the
   template is applied (`.mktmpl` by default).

## How does it work?

Any file ending in `.mktmpl` will have matching `{VARNAME}` strings
replaced with the defined value of `{VARNAME}`. Any file or directory which
name contains `{VARNAME}` will also be expanded using the same rule.

```
$ make
< Make generates Makefile.conf based on all variables ecountered>
< $EDITOR opens Makefile.conf >
< apply:   Once all variables are set, project is expanded in .dist >
< cleanup: mktmpl files are cleaned up and moved to .tmpl …>
< … and .dist files are moved to the current directory >
```

## Similar Projects

[CookieCutter](https://github.com/audreyr/cookiecutter) is a Python tool that
has similar goals. In comparison, `mktmpl` is self-contained and does not need to install an additional software on the system.

[Kickstart](https://github.com/Keats/kickstart) [introduction articl](https://dev.to/artemix/kickstart-a-fast-and-simple-project-bootstrapper-40k1)
