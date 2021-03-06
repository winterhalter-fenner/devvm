# Spryker OS - DevVM - How to release

## Manual release
To manally build the dev vm and prepare `.box` file for the development team,
the process can be started on any machine with VirtualBox and vagrant. If any
configuration has to be made specifically for a project, clone this repository
and change pillar and/or saltstack first.

The commands for preparing box file are following:
```
# Skip cloning Spryker project repository, not needed for VM template
export SPRYKER_REPOSITORY=""
mkdir -p project

# Start the vm using Vagrantfile and run SaltStack provisioning
vagrant up

# Make sure that guest drivers inside VM match VirtualBox version (optional)
vagrant vbguest

# Run server tests (optional)
vagrant ssh -c 'cd /srv/salt/test && sudo rake spec:server'

# Package VM into boxfile with metadata
# Include Vagrantfile-quick as default Vagrantfile inside box
# Resulting file (`package.box`) can be distributed to the team members
vagrant halt
vagrant package --output package.box --vagrantfile Vagrantfile-quick
```

## Automatic releases - Continuous integration and delivery
There is a [CI/CD system](https://jenkins.korekontrol.net/) watching this repository.
It will trigger a build on each commit to `master` and `develop` branch. If it's succesful, the
box file for preview will be available [here](https://jenkins.korekontrol.net/get/spryker/).

If the commit in master has also associated a tag name, it will automatically generate
github release (with the same name as tag) and will publish the vm file there.

Please do not tag if release is not tested. Release candidate tags (`x.y.z-RC*`) should be
tagged on `develop` branch. If a release candidate is approved, the `develop` branch should
be merged into `master` branch and tagged/pushed.

Keep in mind that "just" tagging will not create a new build (if the last commit hash
didn't change). So to make sure that build happens, please do commit + tag and then push.

Sample commands:
```
# Make sure everything is commited
git commit --all

# Create "annotated tag"
git tag -a 'v1.0.3' -m 'v1.0.3'

# Push the code and commit
git push

# Push tags
git push --tags
```
