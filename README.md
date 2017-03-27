Paper pusher
============

A tool to help you move your configuration through environments.

The goal is to reduce diffing between files and providing one canonical
source for your files that you can look at the SCM history to determine
when it was changed and by who.

# NOTE

Readme driven development, so this is the intention of where I want to
end up. Not necessarily what is working right now.

# Use case

Given that you have six different environments that your app has to
traverse before reaching production, and your app requires a different
config file per environment. To handle that manually means making sure
that each value is set and updated for every environment.

For example:

```yaml
---
# local.yml
ENABLE_WORLD_DOMINATION: true
```

```yaml
# production.yml
ENABLE_WORLD_DOMINATION: false
```

To verify that this is configured in all the files between local and
production you'd have to go through all of them manually and see it was
there. And sure, you could `grep` but you would still be
verifying manually.

Instead why not just create one file and generate the correct output files?

```shell
$ cat configuration.yml
---
ENABLE_WORLD_DOMINATION:
    local: true
    # ...
    production: false
$ paper-pusher generate
Generating config files:
    local.yml
# ...
    production.yml
$ cat production.yml
---
ENABLE_WORLD_DOMINATION: false
```

## Usage

```shell
$ cat paper-pusher.yml
configuration:
    renderer: properties
    output_filename: application-{{.env}}.yml
    file: configuration.yml
environments_file: environments.yml

$ paper-pusher generate -c paper-pusher.yml
- application-local.properties
- application-prod.properties
```

# Features

- [ ] Tracking which environments you support:
  If you have six environments that you push your app through, then you
  have six environments. Make sure you have a config value set for all
  of them every time you try to generate a config.
- [ ] Make sure that you have en explicit value for each key. A blank value
  isn't any good for anyone.
- [ ] Add a new environment based on a previous one. Likely your
  QA environment is pretty similar to your new fancy QA2 environment.
- [ ] Support fields that are explicitly blank or secret. Sometimes a valid
  value is just blank and can't be set. And sometimes you can't have
  a secret value in the general repository. Mark them as `<BLANK>` or
  `<SECRET>` respectively and make it a manual routine to fill them in.

# License
MIT
