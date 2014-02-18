---
layout: post
title: "Raking it in"
tagline: "Custom Rakefile tasks for Jekyll"
description: ""
category: "geekery" 
tags: [jekyll, linux, ruby]
date: "Tue, 18 Feb 2014 21:54:23 +0300"
---
{% include JB/setup %}
I'm not lazy, I'm efficient!  To speed publishing, I added these tasks to my Jekyll Rakefile.  I can do almost everything required to add content to this site by issuing a `rake [task]` command.  Hope you find some of them useful!

##rake##

The default task prints a usage message - helpful if I ever forget about some of the shortcuts I've created.

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

##rake update file="filename"##

Changes the file's date: FrontMatter to the specified file's last modified date/time (rfc822 format).  This is useful if you've been drafting a page/post over the course of several days and want it to reflect the actual date of publishing, rather than the file creation date.

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

##rake edit \[path="\_drafts"\]##

Opens the most recently modified file in the specified path for editing with gvim.  The default path is \_posts.  I find this particularly useful when creating new posts, since the post filenames are typically very similar, not fun to type, and bash autocompletion with the tab key only goes so far.

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

##rake build and preview##

Two very simple tasks to build and preview your site using the Jekyll server.  They're almost too simple, but I admit to using the preview task a lot. 

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

##rake commit, push, and deploy##

Automating common git tasks.  Again, these are rather oversimplified.  In fact, you can see where I commented out some of the more elegant [borrowed code](http://davidensinger.com/2013/07/automating-jekyll-deployment-to-github-pages-with-rake) and opted for really bare bones functionality.  The push/deploy tasks will publish all files to the master branch.  So far, I haven't used forks when managing the site, so these tasks do everything I need of them.

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

