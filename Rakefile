require 'rubygems'
require 'rake'

begin
  require 'jeweler'
  Jeweler::Tasks.new do |gem|
    gem.name = 'laser'
    gem.summary = %Q{Analysis and linting tool for Ruby.}
    gem.description = %Q{Laser is an advanced static analysis tool for Ruby.}
    gem.email = 'michael.j.edgar@dartmouth.edu'
    gem.homepage = 'http://github.com/michaeledgar/laser'
    gem.authors = ['Michael Edgar']
    gem.add_development_dependency 'rspec', '~> 2.3'
    gem.add_development_dependency 'yard', '>= 0'
    # gem is a Gem::Specification... see http://www.rubygems.org/read/chapter/20 for additional settings
  end
  Jeweler::GemcutterTasks.new
rescue LoadError
  puts 'Jeweler (or a dependency) not available. Install it with: gem install jeweler'
end

require 'rspec/core/rake_task'
RSpec::Core::RakeTask.new(:spec) do |spec|
  spec.pattern = FileList['spec/**/*_spec.rb']
end

task rcov: :default

require 'rake/testtask'
Rake::TestTask.new do |t|
  t.libs << 'test'
  t.test_files = FileList['test/**/test*.rb']
  t.verbose = true
end

require 'cucumber'
require 'cucumber/rake/task'

Cucumber::Rake::Task.new(:features) do |t|
  t.cucumber_opts = "features --format pretty"
end

require 'metric_fu'

if true
  begin
    require 'laser'
    Laser::Rake::LaserTask.new(:laser) do |laser|
      laser.libs << 'lib' << 'spec'
      laser.using << :all << Laser::LineLengthMaximum(100) << Laser::LineLengthWarning(80)
      laser.options = '--debug --fix'
      laser.fix << Laser::ExtraBlankLinesWarning << Laser::ExtraWhitespaceWarning << Laser::LineLengthWarning(80)
    end
  rescue LoadError => err
    task :laser do
      abort 'Laser is not available. In order to run laser, you must: sudo gem install laser'
    end
  end
end

task rebuild: [:gemspec, :build, :install] do
  %x(rake laser)
end

SRC = FileList['lib/laser/annotation_parser/*.treetop']
OBJ = SRC.sub(/.treetop$/, '_parser.rb')

SRC.each do |source|
  result = source.sub(/.treetop$/, '_parser.rb')
  file result => source do |t|
    sh "tt #{source} -o #{result}"
  end
end

task build_parsers: OBJ

# Alias for script/console from rails world lawlz
task sc: :build_parsers do
  system("irb -r./lib/laser")
end

task spec: :check_dependencies

task default: [:spec, :test]

begin
  require 'yard'
  YARD::Rake::YardocTask.new
rescue LoadError
  task :yardoc do
    abort 'YARD is not available. In order to run yardoc, you must: sudo gem install yard'
  end
end
