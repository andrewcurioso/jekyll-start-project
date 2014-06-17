require 'rake'
require 'yaml'

task :default => :help

desc "display a help message with all available tasks"
task :help do
  puts "Command line tools to make using Jekyll easier.\n\n"
  puts "Usage:\n  rake [action]\n\n"
  system("rake -sT")
end

desc "clean the site output"
task :clean do
  rm_rf '_site'
end

desc "build the Jekyll project"
task :build do
  sh "bundle exec jekyll build"
end

desc "build then deploy to S3"
task :deploy => [ :clean, :build ] do
  sh "bundle exec s3_website push"
end

namespace :config do

  desc "Initializes or updates the project settings"
  task :all => [ :env, :jekyll ]

  def prompt_env name, description, default=""
    print "#{description} [#{default}]: "
    value = STDIN.gets.chomp
    "#{name}=" << (value.length > 0 ? value : default) << "\n"
  end

  desc "Initialize environmental variables in a .env file"
  task :env do
    if File.file?(".env")
      answer = nil
      while answer.nil? || answer.length == 0 || (answer[0].downcase != 'y' && answer[0].downcase != 'n')
        print "A .env file already exists. Overwrite? Y/N "
        answer = STDIN.gets.chomp
      end
      if answer[0].downcase != 'y'
        puts "Skipping .env configuration"
      end
    end

    out = ""
    out << prompt_env("S3_ID", "Amazon S3 ID")
    out << prompt_env("S3_SECRET", "Amazon S3 secret key")
    out << prompt_env("S3_BUCKET", "Amazon S3 bucket name")

    File.open('.env', 'w+') {|f| f.write(out) }
  end

  def prompt_jekyll description, default=""
    print "#{description} [#{default}]: "
    value = STDIN.gets.chomp
    value.length > 0 ? value : default
  end

  desc "Initialize or updates required Jekyll settings"
  task :jekyll do
    d = YAML::load_file('_config.yml')

    options = {
      name: "Site name",
      url:  "Site root URL including http://"
    }

    options.each do |key,description|
      key = key.to_s
      d[key] = prompt_jekyll description, d[key]
    end

    File.open('_config.yml', 'w') {|f| f.write d.to_yaml }
  end

end

desc "alias for config:all"
task :config => :"config:all"
