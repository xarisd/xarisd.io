require 'launchy'
require "yaml"
require 'pp'

desc "Cleans and builds the site"
task :default => [:clean, :build]

desc "Setups the configuration by preparing a file named 'configuration.yaml'"
task :setup do
  puts %Q{
    This task will create a configuration file named 'configuration.yaml'.
    If this file already exists you will be prompted for overwriting it.

    The 'configuration.yaml' file contains all the parameters needed by the rake tasks such as
        * which directories to copy for deployment
        * where to find the presentations
        * your FTP credentials for each environment you setup (i.e. 'production')

    If this is the first time you do this,
    please open the 'configuration.yaml' file with an editor and setup the parameters
  }
  result = `cp -i configuration.example.yaml configuration.yaml`
  open_the_file = ask("Do you want to open the file in vim editor now?", "y", "y", "n")
  system("vim", "configuration.yaml") if open_the_file == 'y'
  # `"${EDITOR:-vim}" configuration.yaml` if open_the_file == 'y'
end

desc "Runs jekyll server. Ctrl + C to stop it"
task :serve do
  `bundle exec jekyll serve`
end

desc "Opens the site in browser (_deploy/index.html)"
task :show do
  Launchy.open('_deploy/index.html')
end

desc "Cleans the \"built\", (removes the _deploy folder)"
task :clean do
  `rm -rf _deploy`
end

desc "Builds the site, which can be found in _deploy"
task :build do
  `bundle exec jekyll build`
end

# Method to get the configuration for building/deploying the site
# requires a configuration.yml file
# You can get one by running "rake setup" or copying it by hand the file "configuration.example.yaml"
def configuration
  configuration_exists = File.exist?("configuration.yaml")
  if configuration_exists
    @configuration ||= YAML.load_file("configuration.yaml")
  else
    @configuration = { "deployment" => {} }
  end
end

# Dynamically create appropriate tasks from the configuration "deployment"
configuration["deployment"].each do | env |
  env_name = env.keys[0]
  env = env[env_name]
  desc "Sets the credentials for '#{env_name} environment (FTP deploy)"
  task :"#{env_name}" do
    @environment = env_name
    @server = env["server"]
    @server_folder = env["server_folder"]
    @user = env["username"]
    @password = env["password"]
    @url = env["url"]
  end
end

desc "Deploys the site via FTP, EXCEPT PRESENTATIONS"
task :deploy => ['deploy:check', :clean, :build] do
  #TODO : This is a poor man's implementation using shell. For a better implementation see # see https://gist.github.com/morgoth/1690647

  built_dir = "_deploy"

  puts "## Deploying website via FTP"
  ftp_address = "ftp://#{@user}:#{@password}@#{@server}/#{@server_folder}/"
  puts ftp_address

  commands = %w[
    prompt
    mput index.html
    mput feed.xml

    delete assets/**.**
    rmdir assets
    mkdir assets
    mput assets/**.**

    delete fonts/**.**
    rmdir fonts
    mkdir fonts
    mput fonts/**.**

    delete about/**.**
    rmdir about
    mkdir about
    mput about/**.**

    delete projects/**.**
    rmdir projects
    mkdir projects
    mput projects/**.**

    mput presentations/index.html

    bye
  ].join("\n")

  puts "=" * 20
  puts commands
  puts "=" * 20

  chdir("_deploy")
  system("echo '#{commands}' | ftp #{ftp_address}")
end

namespace :deploy do
  task :check do
    if @environment == nil
      puts
      puts "Ooops!"
      puts "You neeed to specify a deployment environment first i.e. 'rake production deploy'"
      puts "You can check the available environments with 'rake deploy:environments'"
      puts "If there aren't any, you can specify environments in your 'configuration.yaml' file"
      puts "You can create this file with 'rake setup' or copy the 'configuration.example.yml' file by hand"
      puts "Please do this and try again :-)"
      puts
      abort
    elsif @server == nil || @server_folder == nil || @user == nil || @password == nil
      puts
      puts "Ooops!"
      puts "Some parameters for FTP deployment seem to be missing from your '#{@environment}' environment"
      puts "You can specify these parameters in your 'configuration.yaml' file"
      puts "Please do this and try again :-)"
      puts
      abort
    end
  end

  desc "Displays the available deployment environments (based on configuration.yaml)"
  task :environments do

    lines = []
    rake_commands = []
    configuration["deployment"].each do | env |
      env_name = env.keys[0]
      env = env[env_name]
      lines << ""
      lines << "#{env_name}:"
      lines << "\t#{env.inspect}"
      rake_commands << "\trake #{env_name} deploy"
    end

    puts
    puts
    puts "Displaying deployment environments (based on configuration.yaml)"
    puts "----------------------------------------------------------------"
    puts lines.join("\n")
    puts
    puts
    puts "You can deploy to a specific environment this way : "
    puts rake_commands.join("\n")
    puts
    puts
  end
end

desc "Opens the remote site in a browser"
task :open => ['open:check'] do
  Launchy.open(@url)
end

namespace :open do
  task :check do
    if @environment == nil
      puts
      puts "Ooops!"
      puts "You neeed to specify a deployment environment first i.e. 'rake production open'"
      puts "You can check the available environments with 'rake deploy:environments'"
      puts "If there aren't any, you can specify environments in your 'configuration.yaml' file"
      puts "You can create this file with 'rake setup' or copy the 'configuration.example.yml' file by hand"
      puts "Please do this and try again :-)"
      puts
      abort
    elsif @url == nil
      puts
      puts "Ooops!"
      puts "Yoy have not specified the 'url' parameter for your '#{@environment}' environment"
      puts "You can specify this parameter in your 'configuration.yaml' file"
      puts "Please do this and try again :-)"
      puts
      abort
    end
  end
end

desc "Deploys ONLY THE PRESENTATIONS via FTP"
task :deploy_presentations => [:clean, :build, :copy_presentations] do
  #TODO : For a better implementation see # see https://gist.github.com/morgoth/1690647
  built_dir = "_deploy"

  puts "## Deploying website via FTP"
  ftp_address = "ftp://#{@user}:#{@password}@#{@server}/#{@server_folder}/"
  puts ftp_address

  presentations_dirs = `find ./presentations -type d`
  presentations_dirs_cmds = presentations_dirs.split("\n").map { |pd|
    %Q{
      delete #{pd}/*
      rmdir #{pd}
      mkdir #{pd}
      mput #{pd}/*
    }
  }.join("\n")

  commands = %w[
    prompt
  ].push(presentations_dirs_cmds).push("bye").join(" \n")

  puts "=" * 20
  puts commands
  puts "=" * 20

  chdir("_deploy")
  system("echo '#{commands}' | ftp #{ftp_address}")
end

desc "Copies presentations to /presentations folder"
task :copy_presentations do
  presentations_root = "~/Files/Development/xarisd/presentations"
  presentations = %w[
    ruby-basics-ii
    rails-basics-01
    hacking-presentations
  ]

  `rm -rf presentations`
  `mkdir presentations`
  presentations.each do | pr |
    `rm -rf presentations/#{pr}`
    `mkdir presentations/#{pr}`
    `cp -r #{presentations_root}/#{pr}/presentation/slides/ presentations/#{pr}`
    `mv presentations/#{pr}/presentation.html presentations/#{pr}/index.html`
  end

end

# TODO : Add Documentation comments (YARD)
def ask(question, default_answer, *answers)
  upcase_answers = answers.map { |a| a.to_str.upcase }

  print "#{question} #{answers.join("/")} [#{default_answer}] "

  input = STDIN.gets.chomp
  upcase_input = input.upcase

  if upcase_answers.include?(input.upcase)
    return input
  elsif input == ""
    return default_answer
  else
    puts "Please enter one of the following: #{answers.join("/")} [#{default_answer}]"
    puts "So, I will ask you again"
    ask(question, default_answer, *answers)
  end
end
