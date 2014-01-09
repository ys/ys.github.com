require 'rubygems'
require 'rake'
require 'rdoc'
require 'date'
require 'yaml'
require 'tmpdir'
require 'jekyll'

desc "Generate blog files"
task :generate do
  Jekyll::Site.new(Jekyll.configuration({
    "source"      => ".",
    "destination" => "_site"
  })).process
end


desc "Generate and publish blog to gh-pages"
task :publish => [:generate] do
  Dir.mktmpdir do |tmp|
    system "mv _site/* #{tmp}"
    system "git checkout -b gh-pages"
    system "rm -rf *"
    system "mv #{tmp}/* ."
    message = "Site updated at #{Time.now.utc}"
    system "git add ."
    system "git commit -am #{message.shellescape}"
    system "git push origin gh-pages --force"
    system "git checkout master"
    system "echo yolo"
  end
end

task :default => :publish

namespace :blog do

 task :new, [:title] do |t, args|
   today = Date.today.to_s
   title = args[:title]
   if title.nil? || title == ''
     abort("\nPlease specify a title, eg:\n\nrake blog:new[\"title\"]\n\n")
   end

   slug = title.gsub(/[^a-z0-9\-_]+/i, '-').downcase
   folder = File.join(File.dirname(__FILE__), '_posts')
   filename = File.join(folder, [today, slug].join('-')) + '.markdown'

   config = {
     'layout' => 'large',
     'title' => title,
     'date' => today,
     'categories' => '',
     'background' => '#fff',
     'font_color' => '#000'
   }

   if File.exist?(filename)
     abort("File #{filename} already exists! Aborting.")
   else
     File.open(filename, 'w') do |file|
       file << config.to_yaml
       file << "---\n"
       file << "\n\nPut the next part here\n\n"
     end
   end
 end

end
