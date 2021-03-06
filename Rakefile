require "rubygems"
require 'rake'
require 'yaml'
require 'time'

SOURCE = "."
CONFIG = {
  'version' => "0.3.0",
  'themes' => File.join(SOURCE, "_includes", "themes"),
  'layouts' => File.join(SOURCE, "_layouts"),
  'posts' => File.join(SOURCE, "_posts"),
  'post_ext' => "md",
  'theme_package_version' => "0.1.0"
}

# Path configuration helper
module JB
  class Path
    SOURCE = "."
    Paths = {
      :layouts => "_layouts",
      :themes => "_includes/themes",
      :theme_assets => "assets/themes",
      :theme_packages => "_theme_packages",
      :posts => "_posts"
    }
    
    def self.base
      SOURCE
    end

    # build a path relative to configured path settings.
    def self.build(path, opts = {})
      opts[:root] ||= SOURCE
      path = "#{opts[:root]}/#{Paths[path.to_sym]}/#{opts[:node]}".split("/")
      path.compact!
      File.__send__ :join, path
    end
  
  end #Path
end #JB

# default task - added by yuri
# Usage: rake"
desc "Default task - post usage messages"
task :default do
  puts "Usage: rake"
  puts "  print this message\n\n"
  puts "Usage: rake post title=\"A Title\" [tags=[tag1,tag2]] [category=\"category\"]"
  puts "  create a new post\n\n"
  puts "Usage: rake page name=\"about.html\""
  puts "  create a new page\n\n"
  puts "Usage: rake update file=\"filename\""
  puts "  update file's YAML date: front matter to current system time\n\n"
  puts "Usage: rake edit [path=\"_drafts\"]"
  puts "  edit the most recently modified file in [path] - default is _posts/*\n\n"
  puts "Usage: rake build"
  puts "  build site using jekyll\n\n"
  puts "Usage: rake preview"
  puts "  start jekyll site server at localhost:4000\n\n"
  puts "Usage: rake theme:switch name=\"the-program\""
  puts "  switch between jekyll-bootstrap themes\n\n"
  puts "Usage: rake theme:install git=\"https://github.com/jekyllbootstrap/theme-twitter.git\""
  puts "Usage: rake theme:install name=\"cool-theme\""
  puts "  install new jekyll-bootstrap theme\n\n"
  puts "Usage: rake commit"
  puts "  stage files for git upload\n\n"
  puts "Usage: rake push"
  puts "  push (uploads) staged files to GitHub\n\n"
  puts "Usage: rake deploy"
  puts "  execute commit and push tasks to deploy (upload) site changes"
end # default task

# Usage: rake post title="A Title" [tags=[tag1,tag2]] [category="category"]
desc "Begin a new post in #{CONFIG['posts']}"
task :post do
  abort("rake aborted: '#{CONFIG['posts']}' directory not found.") unless FileTest.directory?(CONFIG['posts'])
  title = ENV["title"] || "new-post"
  tags = ENV["tags"] || "[]"
  category = ENV["category"] || ""
  category = "\"#{category.gsub(/-/,' ')}\"" if !category.empty?
  slug = title.downcase.strip.gsub(' ', '-').gsub(/[^\w-]/, '')
  # yuri changed date to hard coded Time.now rather than user optioned
  systime = Time.now
  date = systime.strftime('%Y-%m-%d')
  filename = File.join(CONFIG['posts'], "#{date}-#{slug}.#{CONFIG['post_ext']}")
  if File.exist?(filename)
    abort("rake aborted!") if ask("#{filename} already exists. Do you want to overwrite?", ['y', 'n']) == 'n'
  end
  
  puts "Creating new post: #{filename}"
  open(filename, 'w') do |post|
    post.puts "---"
    post.puts "layout: post"
    post.puts "title: \"#{title.gsub(/-/,' ')}\""
    post.puts 'description: ""'
    post.puts "category: #{category}"
    post.puts "tags: #{tags}"
    # yuri added rfc822 date front matter
    post.puts "date: \"#{systime.rfc822}\""
    post.puts "---"
    post.puts "{% include JB/setup %}"
  end
end # task :post

# Usage: rake page name="about.html"
# You can also specify a sub-directory path.
# If you don't specify a file extention we create an index.html at the path specified
desc "Create a new page."
task :page do
  name = ENV["name"] || "new-page.md"
  filename = File.join(SOURCE, "#{name}")
  filename = File.join(filename, "index.html") if File.extname(filename) == ""
  title = File.basename(filename, File.extname(filename)).gsub(/[\W\_]/, " ").gsub(/\b\w/){$&.upcase}
  if File.exist?(filename)
    abort("rake aborted!") if ask("#{filename} already exists. Do you want to overwrite?", ['y', 'n']) == 'n'
  end
  
  mkdir_p File.dirname(filename)
  puts "Creating new page: #{filename}"
  open(filename, 'w') do |post|
    post.puts "---"
    post.puts "layout: page"
    post.puts "title: \"#{title}\""
    post.puts 'description: ""'
    # yuri added rfc822 date front matter
    post.puts "date: \"#{Time.now.rfc822}\""
    post.puts "---"
    post.puts "{% include JB/setup %}"
  end
end # task :page

# update page/post YAML frontmatter (publish) date to the file's last modified date/time
# added by yuri
# will not add "date: " frontmatter - only updates the existing one
# Usage: rake update file="filename"
desc "Update YAML date"
task :update do
  filename = ENV["file"]
  if filename.nil?
    abort("Usage: rake update file=\"filename\"")
  end
  if not File.exist?(filename)
    abort("File not found.")
  end
  if open(filename).grep(/^date: ".*"/).length > 0
    mdatetime = File.mtime(filename).rfc822
    text = File.read(filename).gsub(/^date: ".*"/, "date: \"#{mdatetime}\"")
    File.open(filename, "w").write(text)
    puts "Updated publish date to #{mdatetime}"
  else
    puts "File not updated -- no [date: ] FrontMatter found."
  end
end # task update

# edit (in gvim) the most recently modified file - added by yuri
# useful after creating a new post or page
# Usage: rake edit [path="_drafts"]
desc "Edit the most recently modified file with gvim"
task :edit do
  path = ENV["path"] || "_posts"
  file = Dir.glob("#{path}/*").max_by {|f| File.mtime(f)}
  status = system("gvim #{file}")
  puts status ? "Opening #{file} with gvim" : "Failed to open #{file}"
end # task edit

# jekyll build - added by yuri
# Usage: rake build"
desc "Build local site"
task :build do
  system "jekyll build"
end # task build

# jekyll site preview - added by yuri
desc "Launch preview environment"
task :preview do
  system "jekyll serve -w"
end # task :preview

# Public: Alias - Maintains backwards compatability for theme switching.
task :switch_theme => "theme:switch"

namespace :theme do
  
  # Public: Switch from one theme to another for your blog.
  #
  # name - String, Required. name of the theme you want to switch to.
  #        The theme must be installed into your JB framework.
  #
  # Examples
  #
  #   rake theme:switch name="the-program"
  #
  # Returns Success/failure messages.
  desc "Switch between Jekyll-bootstrap themes."
  task :switch do
    theme_name = ENV["name"].to_s
    theme_path = File.join(CONFIG['themes'], theme_name)
    settings_file = File.join(theme_path, "settings.yml")
    non_layout_files = ["settings.yml"]

    abort("rake aborted: name cannot be blank") if theme_name.empty?
    abort("rake aborted: '#{theme_path}' directory not found.") unless FileTest.directory?(theme_path)
    abort("rake aborted: '#{CONFIG['layouts']}' directory not found.") unless FileTest.directory?(CONFIG['layouts'])

    Dir.glob("#{theme_path}/*") do |filename|
      next if non_layout_files.include?(File.basename(filename).downcase)
      puts "Generating '#{theme_name}' layout: #{File.basename(filename)}"

      open(File.join(CONFIG['layouts'], File.basename(filename)), 'w') do |page|
        if File.basename(filename, ".html").downcase == "default"
          page.puts "---"
          page.puts File.read(settings_file) if File.exist?(settings_file)
          page.puts "---"
        else
          page.puts "---"
          page.puts "layout: default"
          page.puts "---"
        end 
        page.puts "{% include JB/setup %}"
        page.puts "{% include themes/#{theme_name}/#{File.basename(filename)} %}" 
      end
    end
    
    puts "=> Theme successfully switched!"
    puts "=> Reload your web-page to check it out =)"
  end # task :switch
  
  # Public: Install a theme using the theme packager.
  # Version 0.1.0 simple 1:1 file matching.
  #
  # git  - String, Optional path to the git repository of the theme to be installed.
  # name - String, Optional name of the theme you want to install.
  #        Passing name requires that the theme package already exist.
  #
  # Examples
  #
  #   rake theme:install git="https://github.com/jekyllbootstrap/theme-twitter.git"
  #   rake theme:install name="cool-theme"
  #
  # Returns Success/failure messages.
  desc "Install theme"
  task :install do
    if ENV["git"]
      manifest = theme_from_git_url(ENV["git"])
      name = manifest["name"]
    else
      name = ENV["name"].to_s.downcase
    end

    packaged_theme_path = JB::Path.build(:theme_packages, :node => name)
    
    abort("rake aborted!
      => ERROR: 'name' cannot be blank") if name.empty?
    abort("rake aborted! 
      => ERROR: '#{packaged_theme_path}' directory not found.
      => Installable themes can be added via git. You can find some here: http://github.com/jekyllbootstrap
      => To download+install run: `rake theme:install git='[PUBLIC-CLONE-URL]'`
      => example : rake theme:install git='git@github.com:jekyllbootstrap/theme-the-program.git'
    ") unless FileTest.directory?(packaged_theme_path)
    
    manifest = verify_manifest(packaged_theme_path)
    
    # Get relative paths to packaged theme files
    # Exclude directories as they'll be recursively created. Exclude meta-data files.
    packaged_theme_files = []
    FileUtils.cd(packaged_theme_path) {
      Dir.glob("**/*.*") { |f| 
        next if ( FileTest.directory?(f) || f =~ /^(manifest|readme|packager)/i )
        packaged_theme_files << f 
      }
    }
    
    # Mirror each file into the framework making sure to prompt if already exists.
    packaged_theme_files.each do |filename|
      file_install_path = File.join(JB::Path.base, filename)
      if File.exist? file_install_path and ask("#{file_install_path} already exists. Do you want to overwrite?", ['y', 'n']) == 'n'
        next
      else
        mkdir_p File.dirname(file_install_path)
        cp_r File.join(packaged_theme_path, filename), file_install_path
      end
    end
    
    puts "=> #{name} theme has been installed!"
    puts "=> ---"
    if ask("=> Want to switch themes now?", ['y', 'n']) == 'y'
      system("rake switch_theme name='#{name}'")
    end
  end

  # Public: Package a theme using the theme packager.
  # The theme must be structured using valid JB API.
  # In other words packaging is essentially the reverse of installing.
  #
  # name - String, Required name of the theme you want to package.
  #        
  # Examples
  #
  #   rake theme:package name="twitter"
  #
  # Returns Success/failure messages.
  desc "Package theme"
  task :package do
    name = ENV["name"].to_s.downcase
    theme_path = JB::Path.build(:themes, :node => name)
    asset_path = JB::Path.build(:theme_assets, :node => name)

    abort("rake aborted: name cannot be blank") if name.empty?
    abort("rake aborted: '#{theme_path}' directory not found.") unless FileTest.directory?(theme_path)
    abort("rake aborted: '#{asset_path}' directory not found.") unless FileTest.directory?(asset_path)
    
    ## Mirror theme's template directory (_includes)
    packaged_theme_path = JB::Path.build(:themes, :root => JB::Path.build(:theme_packages, :node => name))
    mkdir_p packaged_theme_path
    cp_r theme_path, packaged_theme_path
    
    ## Mirror theme's asset directory
    packaged_theme_assets_path = JB::Path.build(:theme_assets, :root => JB::Path.build(:theme_packages, :node => name))
    mkdir_p packaged_theme_assets_path
    cp_r asset_path, packaged_theme_assets_path

    ## Log packager version
    packager = {"packager" => {"version" => CONFIG["theme_package_version"].to_s } }
    open(JB::Path.build(:theme_packages, :node => "#{name}/packager.yml"), "w") do |page|
      page.puts packager.to_yaml
    end
    
    puts "=> '#{name}' theme is packaged and available at: #{JB::Path.build(:theme_packages, :node => name)}"
  end
  
end # end namespace :theme

# Internal: Download and process a theme from a git url.
# Notice we don't know the name of the theme until we look it up in the manifest.
# So we'll have to change the folder name once we get the name.
#
# url - String, Required url to git repository.
#        
# Returns theme manifest hash
def theme_from_git_url(url)
  tmp_path = JB::Path.build(:theme_packages, :node => "_tmp")
  abort("rake aborted: system call to git clone failed") if !system("git clone #{url} #{tmp_path}")
  manifest = verify_manifest(tmp_path)
  new_path = JB::Path.build(:theme_packages, :node => manifest["name"])
  if File.exist?(new_path) && ask("=> #{new_path} theme package already exists. Override?", ['y', 'n']) == 'n'
    remove_dir(tmp_path)
    abort("rake aborted: '#{manifest["name"]}' already exists as theme package.")
  end

  remove_dir(new_path) if File.exist?(new_path)
  mv(tmp_path, new_path)
  manifest
end

# Internal: Process theme package manifest file.
#
# theme_path - String, Required. File path to theme package.
#        
# Returns theme manifest hash
def verify_manifest(theme_path)
  manifest_path = File.join(theme_path, "manifest.yml")
  manifest_file = File.open( manifest_path )
  abort("rake aborted: repo must contain valid manifest.yml") unless File.exist? manifest_file
  manifest = YAML.load( manifest_file )
  manifest_file.close
  manifest
end

def ask(message, valid_options)
  if valid_options
    answer = get_stdin("#{message} #{valid_options.to_s.gsub(/"/, '').gsub(/, /,'/')} ") while !valid_options.include?(answer)
  else
    answer = get_stdin(message)
  end
  answer
end

def get_stdin(message)
  print message
  STDIN.gets.chomp
end

# rake commit/deploy - added by yuri
# from http://davidensinger.com/2013/07/automating-jekyll-deployment-to-github-pages-with-rake/

desc "Commit _site/"
task :commit do
  puts "\n## Staging modified files"
  status = system("git add -A")
  puts status ? "Success" : "Failed"
  puts "\n## Committing a site build at #{Time.now.utc}"
  message = "Build site at #{Time.now.utc}"
  status = system("git commit -m \"#{message}\"")
  puts status ? "Success" : "Failed"
 # puts "\n## Pushing commits to remote"
 # status = system("git push origin source")
 # puts status ? "Success" : "Failed"
end

desc "Push _site/ to master branch"
task :push do
#  puts "\n## Deleting master branch"
#  status = system("git branch -D master")
#  puts status ? "Success" : "Failed"
#  puts "\n## Creating new master branch and switching to it"
#  status = system("git checkout -b master")
#  puts status ? "Success" : "Failed"
#  puts "\n## Forcing the _site subdirectory to be project root"
#  status = system("git filter-branch --subdirectory-filter _site/ -f")
#  puts status ? "Success" : "Failed"
#  puts "\n## Switching back to source branch"
#  status = system("git checkout source")
#  puts status ? "Success" : "Failed"
  puts "\n## Pushing all branches to origin"
  status = system("git push --all origin")
  puts status ? "Success" : "Failed"
end

desc "Commit and deploy _site/"
task :deploy=> [:commit, :push] do
end

#Load custom rake scripts
Dir['_rake/*.rake'].each { |r| load r }
