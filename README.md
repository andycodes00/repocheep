# Repocheep <small>a reprepro python wrapper</small>

![](https://raw.github.com/andrewbunday/repocheep/master/icon.png)

## What's it For?

Repocheep is designed to be a pretty light weight Python wrapper onto the command line tool [Reprepro](http://mirrorer.alioth.debian.org/).

Reprepo can be used to build and manage a local repositories, one of which we use internally to host software and tools which we deploy onto our production hosts.

Our internal build chain is built using bash and python, and for programatic access to reprepro we wanted to have a python module which would handle:

* Subprocess handling, communication and return codes.
* Add additional queries to the repository which cannot be accessed solely through
  reprepro.
* Generate consistant data structures which we could use in other projects.

## Simple Usage

Start by importing the module and create a Repository() handler object. You pass the path to the Base directory of the repository in as the first argument.

```python
import repocheep
repository =repocheep.Repository('/path/to/my/repository')
assert repository.check()
```

_The method `check()` performs the action __check__ on the repository._

The following set of examples demonstrate the query based actions available:

#### Listing Repository Structure

```python
codenames = repository.list_dists()
```

List each of the distributions in the repository. Returns a list of dicts() containing the codenames of the distributions().


```python
components = repository.list_components(codename)
```

List each of the components in the repository for a given distribution's codename. Returns a list of dicts().


```python
archs = repository.list_architectures(codename)
```

List each of the architectures in the repository for a given distribution's codename. Returns a list of dicts() containing the component name and codename.


#### Listing Packages

```python
packages = repository.ls(package_name)
```

Returns a list of _all_ of the versions of a given package found across all of the distributions in the repository.


 ```python
 packages = repository.list(codename, package_name=None)
 ```

Returns a list of all of the matching packages within a given distribtion. If no package name is given it will return all of the packages in the distribution. This can be used with .list_dists() to find all of the packages in the repository.

 ```python
 packages = repository.listmatched(codename, '*maya*')
 ```

Same as the .list() method, but matches against glob style patterns.

```python
references = repository.dumpreferences()
```

Returns a list of all of the packages currently referenced by a  codename/component/architecture key.

```python
changelog = repository.list_changes(debfile)
```

Uses `debian_bundle.DebFile` to extract the changelog from the debfile. __WARNING__ - this method is blocking and can take a long time to return depending on the size of the archive.

### Applying Filters

Reprepro has options which can be set to control the output of some of the actions.
For example `dumpreferences()`  will by default list all of the references in the repository. If we only wanted to see those for a particular component/s these can be set on the options attribute.

...
```python
    repository.options.components = [componentA, componentB]
    repository.options.architectures = [archA, archB]

    references = repository.dumpreferences()
```
