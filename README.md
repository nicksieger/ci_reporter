CI::Reporter is an add-on to Ruby testing frameworks like Test::Unit
or RSpec that allows you to generate XML reports of your test
runs. The resulting files can be read by a continuous integration
system that understands Ant's JUnit report XML format, thus allowing
your CI system to track test/spec successes and failures.

[![Build Status](https://travis-ci.org/nicksieger/ci_reporter.svg?branch=master)](https://travis-ci.org/nicksieger/ci_reporter)

## Usage

CI::Reporter works with projects that use standard Rake tasks for
running tests. In this fashion, it hooks into testing frameworks using
environment variables recognized by these custom tasks to inject the
CI::Reporter code into the test run.

Each supported testing framework is provided by a separate gem:

* [Cucumber][ci-cuke]
* [Minitest][ci-mt]
* [RSpec][ci-rspec]
* [Spinach][ci-spin]
* [Test::Unit][ci-tu]

[ci-cuke]: https://github.com/ci-reporter/ci_reporter_cucumber
[ci-mt]: https://github.com/ci-reporter/ci_reporter_minitest
[ci-rspec]: https://github.com/ci-reporter/ci_reporter_rspec
[ci-spin]: https://github.com/ci-reporter/ci_reporter_spinach
[ci-tu]: https://github.com/ci-reporter/ci_reporter_test_unit

## Jenkins setup

1. Tick the box labelled "Publish JUnit test result report" in the job
configuration

2. Enter "test/reports/*.xml,spec/reports/*.xml" in the "Test report
XMLs" field (adjust this to suit which tests you are running)

Report files are written, by default, to the
<code>test/reports</code>, <code>features/reports</code> or
<code>spec/reports</code> subdirectory of your project.  If you wish
to customize the location, simply set the environment variable
CI_REPORTS (either in the environment, on the Rake command line, or in
your Rakefile) to the location where they should go.

## Conditional reporting

You may not wish to always produce report files. There are two primary
ways to configure this:

### With environment variables

Use an environment variable in your Rakefile to control if CI:Reporter
will be invoked:

```ruby
if ENV['GENERATE_REPORTS'] == 'true'
  require 'ci/reporter/rake/rspec'
  task :rspec => 'ci:setup:rspec'
end
```

You can either inject this variable in your CI or simply call `rake`
with the environment variable set:

```
GENERATE_REPORTS=true rake rspec
```

### With CI-specific Rake tasks

Instead of modifying your existing Rake tasks, create new ones:

```ruby
namespace :ci do
  task :all => ['ci:setup:rspec', 'rspec']
end
```

Then use this Rake target in CI:

```
rake ci:all
```

## Environment Variables

* `CI_REPORTS`: if set, points to a directory where report files will
  be written.
* `CI_CAPTURE`: if set to value "off", stdout/stderr capture will be
  disabled.