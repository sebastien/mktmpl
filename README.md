```
         _   _             _ 
   _____| |_| |_ _____ ___| |
  |     | '_|  _|     | . | |
  |_|_|_|_,_|_| |_|_|_|  _|_|
                      |_|    
```

# *mktmpl* ― Makefile-based templates

*mktmpl* is a self-contained `Makefile` that generates project files
given a template. Compared to simliar tools, *mktmpl* does not require
to install any package or tool besides what you'd find a regular Unix
development environment.

Here are the main features:

- Zero-requirement (besides a standard Unix development environment)
- Language agnostic
- Self-contained

## Quickstart

### Creating and publishing a template

Clone the `mktmpl` repository

```
$ git clone git@github.com:sebastien/mktmpl.git YOUR_NEW_TEMPLATE
$ cd YOUR_NEW_TEMPLATE
```  

You can now populate your template files and directories in `tmpl`. You can use
template variables like `{VARIABLE}` in your file and directory names, or in the
content of `.tmpl` files.

These *template variables* will be automatically detected and expanded to the
value set in the `Makefile.conf` by the user. Files ending in `.tmpl` will
have their suffix dropped.

``` 
$ mkdir tmpl
$ echo "# {PROJECT} Readme" > tmpl/README.tmpl
$ mkdir 'tmpl/src/{LANG}/{PROJECT}'
$ echo "# This is the main file for {PROJECT}" > 'tmpl/src/{LANG}/{PROJECT}/main.{LANG}'
$ vi README.md
```

If you'd like to see the content of your template, run `make manifest`.
At any point you can test your template by running `make apply`. This will
ask you to edit a configuration file using `$EDITOR` (or `vi`) and will
populate `.dist` with the applied `tmpl` files and directories.

Once you're done, you simply need to add the `tmpl` files to the repository
and publish it. That's it!

### Using a template

If you'd like to create a new project from a *mktmpl* template, you
would simply need to do the following:

```
$ git clone YOUR_TEMPLATE_REPOSITORY YOUR_NEW_PROJECT
$ cd YOUR_NEW_PROJECT
$ make
```

The first run of `make` will pop-up your `$EDITOR` and ask you to fill in the
main variables for the project.

If successful, the original template files will be moved to the `.tmpl`
directory within your project. If you'd like to re-generate the project, run
`pushd .tmpl ; make revert ; popd` and then either `make config` to change
configuration options or `make` to rebuild everything using the same configuration.

## Makefile rules 

The following rules are available up until the moment you `make cleanup`:

- `make all` ― The default rule that does `make apply` followed by `make cleanup`

- `make manifest|mf` ― Lists the files that are part of the project template. 

- `make variables|vars` ― Lists the variables defined 

- `make configure|config` ― Creates the `Makefile.conf` (or `$TEMPLATE_CONF`) file and runs
  `$EDITOR` on it. Once the editor quits, the makefile will try to 
   apply the configuration to the templates.

- `make apply` ­― Applies the configuration to produce the templates in the
  `$PRODUCT_PATH?=.dist` directory. All the files in
  `tmpl` will be expanded using the configuration and written
  to `.dist`.

- `make cleanup` ― Cleans up the *mktmpl* files and moves all the `.dist`
  files within the current directory. The *mktmpl* files
  are then backed up to `.tmpl`. You can revert everything
  by `cd .tmpl ; make revert`.

- `make revert` ―  Reverts a `make cleanup`, returning to the result
   of `make apply` in the parent directory.

- `make rules` ― Lists the generated makefile rules  that produce the applied
   template files. This is mostly useful for debugging.

## Makefile variables

These are variables that you can override if you need to change some of the
paths or extensions used by the main *mktmpl* makefile.

- `TEMPLATE_OUTPUT`: The path where the template should be output. By default, this is
  the folder in which the template makefile is located.

- `TEMPLATE_CONF`: The name of the makefile template configuration file where the 
  template variable's values is going to be stored.

- `TEMPLATE_PATH`: The path where the template sources/files are located. By default,
  it is `tmpl`

- `TEMPLATE_BACKUP`: The path where the original template directory will be moved once the
   template is applied (`.tmpl` by default).

## How does it work?

Template variables are defined as a `VARNAME:=VALUE` mapping in `Makefile.conf`, which
is dynamically loaded when the *mktmpl* Makefile is run.

Any file ending in `.tmpl` will have matching `{VARNAME}` strings
in its contents replaced with the coreresponding value in `Makefile.conf`.
Any file or directory which path contains `{VARNAME}` will also be expanded using the same rule.

Here's what happens when running `make` or `make all`:

1) *configuration phase*: `make` generates `Makefile.conf` based on all variables ecountered, $EDITOR` opens `Makefile.conf`
2) *apply phase*:  if all variables are set, the templates are expanded in `.dist`
3) *cleanup phase*: the *mktmpl* files are moved to `.tmpl` and the contents of `.dist` is moved 
   in the current directory.

## Similar Projects

There are a few similar projects:

- [CookieCutter](https://github.com/audreyr/cookiecutter) is a Python tool that
has similar goals and more features.

- [Kickstart](https://github.com/Keats/kickstart), described [here](https://dev.to/artemix/kickstart-a-fast-and-simple-project-bootstrapper-40k1) 
supports conditional templates and interactive configuration options.

- [Tinpig](https://github.com/bit101/tinpig), has a centralized template respository and an interactive CLI.

Compared to these *mktmpl* does not have the user-friendly interactive prompt to define
conifguration variables (instead it edits the makefile configuration), nor does it have
conditional templates (based on how you answered your questions). If you don't need these
features and prefer a self-contained, simple alternative that integrates well with `make`,
*mktmpl* might be the better option.
