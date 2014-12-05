require 'launchy'
require "yaml"

desc "Cleans and builds the site"
task :default => [:clean, :build]

desc "Builds the site, which can be found in _deploy"
task :build do
  `bundle exec jekyll build`
end

desc "Cleans the \"built\", (removes the _deploy folder)"
task :clean do
  `rm -rf _deploy`
end

desc "Opens the site in browser (_deploy/index.html)"
task :show do
  Launchy::Browser.run('_deploy/index.html')
end

desc "Runs jekyll server. Ctrl + C to stop it"
task :serve do
  `bundle exec jekyll serve`
end


# Method to get the credentials for production
# required credentials.yml file, that contains keys:
# production>(server, username, password)
def credentials
  @credentials ||= YAML.load_file("credentials.yaml")
end

desc "Sets the credentials for production (FTP deploy)"
task :production do
  @server = credentials["production"]["server"]
  @server_folder = credentials["production"]["server_folder"]
  @user = credentials["production"]["username"]
  @password = credentials["production"]["password"]
end

desc "Deploys the site via FTP, EXCEPT PRESENTATIONS"
task :deploy => [:clean, :build] do
  #TODO : For a better implementation see # see https://gist.github.com/morgoth/1690647
  built_dir = "_deploy"

  puts "## Deploying website via FTP"
  ftp_address = "ftp://#{@user}:#{@password}@#{@server}/#{@server_folder}/"
  puts ftp_address

  # presentations_dirs = `find ./presentations -type d`
  # presentations_dirs_cmds = presentations_dirs.split("\n").map { |pd|
  #   %Q{
  #     delete #{pd}/*
  #     rmdir #{pd}
  #     mkdir #{pd}
  #     mput #{pd}/*

  #   }
  # }.join("\n")

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
   # push(presentations_dirs_cmds).push("bye").join(" \n")

  puts "=" * 20
  puts commands
  puts "=" * 20

  chdir("_deploy")
  system("echo '#{commands}' | ftp #{ftp_address}")
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
