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

desc "Deploys the site via FTP"
task :deploy => [:clean, :build] do

  #TODO : For a better implementation see # see https://gist.github.com/morgoth/1690647

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


    delete presentations/**.**
    rmdir presentations
    mkdir presentations
    mput presentations/**.**

    delete projects/**.**
    rmdir projects
    mkdir projects
    mput projects/**.**

    bye
  ].join(" \n")

  chdir("_deploy")
  system("echo '#{commands}' | ftp #{ftp_address}")

end
