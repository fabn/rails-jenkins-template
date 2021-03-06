## What is it?
This is a template to be used in Jenkins CI in order to build a Ruby On Rails project

## Required hudson plugins
* [Ruby metrics plugin](http://wiki.jenkins-ci.org/display/JENKINS/Ruby+Metrics+Plugin)
* [Rake plugin](Jenkins Rake plugin)
* [Html Publisher](http://wiki.hudson-ci.org/display/HUDSON/HTML+Publisher+Plugin)

## Required Ruby gems
* [ci\_reporter](https://github.com/nicksieger/ci_reporter)
* [metrical](https://github.com/iain/metrical)

## Usage

After you've installed require gems in your project test the following commands are working in your environment

		rake db:migrate db:test:prepare
		rake ci:setup:rspec spec:rcov
		rake CUCUMBER_OPTS='--format html --out features/reports/features.html' cucumber
		metrical
		
You should edit your `Rakefile` to make some of them working by adding the following lines

		# used to generate ci reports, see ci_reporter documentation
		require 'ci/reporter/rake/rspec'

Finally you should add metrical configuration in `.metric` file in your project, this is the mine

		# used to generate metrics, see metric_fu documentation
		MetricFu::Configuration.run do |config|
		  config.metrics = [:churn, :flog, :flay, :reek, :roodi, :hotspots, :rails_best_practices]
		  config.metrics << :saikuro unless RUBY_VERSION == '1.9.2'
		  config.graphs = [:flog, :flay, :reek, :roodi, :rails_best_practices]
		end

I excluded rcov from `metric_fu` configuration because coverage task is included in Ruby Metrics Plugin.

Then in order to configure Jenkins:

1. Install required Jenkins plugins using the plugin manager
2. Make [rvm](https://rvm.beginrescueend.com/) available to the Jenkins user
3. Install at least one Ruby for the jenkins user and set it to default with `rvm use <ruby> --default`, where `<ruby>` is the version you've installed.
3. Install `metrical` gem for the jenkins user (`rvm use default && gem install metrical`)
2. Install this template in Jenkins by `cd $JENKINS_HOME/jobs && git clone git://github.com/fabn/rails-jenkins-template.git rails-template`
3. Restart Jenkins to load the new job template
3. Click on "New Job"
4. Enter a "Job Name"
5. Select "Copy existing job" and enter "rails-template" into the "Copy from" field.
6. Click "OK"
7. Disable the "Disable Build" option
8. Fill in your "Source Code Management" information
9. Edit project options as your needs (i.e. disabling some steps or reports)
9. Click "Save"

## Thanks to

* Sebastian Bergmann for the idea [http://jenkins-php.org/](http://jenkins-php.org)