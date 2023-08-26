# Amazon Deadline Cloud for Nuke Development

To develop this package, you'll need Python with `hatch` installed to start.

## Build the package.
```
hatch build
```

## Run tests
```
hatch run test
```

## Run linting
```
hatch run lint
```

## Run formating
```
hatch run fmt
```

## Run a full release test.
```
hatch run all:test
```

# Submitter Development Workflow

Here are steps you can follow to run and edit the source code within your Nuke installation on Windows. The steps
will work on Linux or MacOS if you modify the path references as appropriate.

WARNING: This workflow installs additional Python packages into your Nuke's python distribution.

1. Create a development location within which to do your git checkouts. For example `~/deadline-clients`.
   Clone packages from this directory with commands like
   `git clone git@github.com:casillas2/deadline-nuke.git`. You'll also want the `deadline` repo.
2. Switch to your Nuke directory, like `cd "C:\Program Files\Nuke13.2v4"`.
3. Run `.\python -m pip install -e C:\Users\<username>\deadline` to install the Amazon Deadline Cloud Client
   Library in edit mode.
4. Run `.\python -m pip install -e C:\Users\<username>\deadline-nuke` to install the Nuke Submtiter
   in edit mode.
6. Run `set NUKE_PATH=C:\Users\<username>\deadline-clients\deadline-nuke\src` to put the `menu.py`
   file in the path Nuke searches for menu extensions.
7. Run `set DEADLINE_ENABLE_DEVELOPER_OPTIONS=true` to enable the job bundle debugging support.
   This enables a menu item you can use to run the tests from the `job_bundle_output_tests` directory.
8. Run `.\Nuke<version>.exe` to run Nuke. The Nuke submitter should be available in the Thinkbox menu.

# Application Interface Adaptor Development Workflow

You can work on the adaptor alongside your submitter development workflow using a Deadline Cloud
farm that uses a service-managed fleet. You'll need to perform the following steps to subsititute
your build of the adaptor for the one in the service.

1. Use the development location from the Submitter Development Workflow.
   You will need to also check out `openjobio` from git if you do
   not already have it. Make sure you're running Nuke with `set DEADLINE_ENABLE_DEVELOPER_OPTIONS=true`
   enabled.
2. Build wheels for `openjobio`, `deadline` and `deadline-nuke`.
   ```
   # If you don't have the build package installed already
   $ pip install build
   ...
   $  mkdir wheels; \
      rm wheels/*; \
      for dir in ../openjobio ../deadline ../deadline-nuke; \
        do python -m build --wheel --outdir ./wheels --skip-dependency-check $dir; \
      done
   ...
   $ ls ./wheels
   openjobio-<version>-py3-none-any.whl
   deadline_nuke-<version>-py3-none-any.whl  deadline-<version>-py3-none-any.whl
   ```
3. Open the Nuke integrated submitter, and in the Job-Specific Settings tab, enable the option 'Include Adaptor Wheels'. This
   option is only visible when the environment variable `DEADLINE_ENABLE_DEVELOPER_OPTIONS` is set to `true`.
   Then submit your test job.