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

A typical workflow is like this:

```
$ git clone REPOSITORY PROJECT
$ cd PROJECT
$ make
```

The first run of `make` will pop-up your `$EDITOR` and ask you to fill-in the
main variables for the project. If everything worked fine, the original makefile
will be removed and the templates will be expanded.

The original template files will be moved to the `.tmpl` directory within your project. If 
you'd like to re-generate the project, simply `cd .tmpl` and generate the project again or
move the `.tmpl` directory somwhere else.

## Makefile rules 


### `make manifest`, `make mf`

Lists the files that are part of the project template. 

### `make variables`, `make vars`

Lists the variables defined 

### `make configure`, `make config`

Creates the `Makefile.conf` (or `$TEMPLATE_CONF`) file and runs
`$EDITOR` on it. Once the editor quits, the makefile will try to 
apply the configuration to the templates.

### `make rules`

## Makefile variables

### `TEMPLATE_OUTPUT`

The path where the template should be output. By default, this is
the folder in which the template makefile is located.

### `TEMPLATE_CONF`

The name of the makefile template configuration file where the 
template variable's values is going to be stored.

### `TEMPLATE_PATH`

The path where the template sources/files are located. By default,
it is `tmpl`

### `TEMPLATE_BACKUP`

The path where the original template directory will be moved once the
tempate is applied (`.mktmpl` by default).

## How does it work?

Any file ending in `.mktmpl` will have matching `{VARNAME}` strings
replaced with the defined value of `{VARNAME}`. Any file or directory which
name contains `{VARNAME}` will also be expanded using the same rule.

```
$ make
< Make generates Makefile.conf based on all variables ecountered>
< $EDITOR opens Makefile.conf >
< Once all variables are set, project is expanded >
```

## Similar Projects

[CookieCutter](https://github.com/audreyr/cookiecutter) is a Python tool that
has similar goals. In comparison, `mktmpl` is self-contained and does not need to install an additional software on the system.

[Kickstart](https://github.com/Keats/kickstart) [introduction articl](https://dev.to/artemix/kickstart-a-fast-and-simple-project-bootstrapper-40k1)
