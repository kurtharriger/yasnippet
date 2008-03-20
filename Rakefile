# -*- Ruby -*-

require 'fileutils'

def find_version
  File.read("yasnippet.el") =~ /;; Version: *([0-9.]+) *$/
  $version = $1
end
find_version
FileUtils.mkdir_p('pkg')

desc "generate the bundle file."
task :bundle do
  sh 'emacs --batch -l yasnippet.el --eval "(yas/compile-bundle)"'
  sh "tar czf pkg/yasnippet-bundle-#{$version}.el.tgz yasnippet-bundle.el"
end

desc "create a release package"
task :package do
  release_dir = "pkg/yasnippet-#{$version}"
  FileUtils.mkdir_p(release_dir + "/doc")
  files = ['snippets', 'yasnippet.el']
  FileUtils.cp_r files, release_dir
  Dir['doc/*.html'].each { |f|
    FileUtils.cp f, release_dir + '/doc' + f.sub(/^doc/, "")
  }
  FileUtils.rm_r Dir[release_dir + "/**/.svn"]
  FileUtils.cd 'pkg'
  sh "tar cjf yasnippet-#{$version}.tar.bz2 yasnippet-#{$version}"
  FileUtils.cd ".."
end

desc "create a release package and upload it to google code"
task :release => [:bundle, :package] do
  version = find_version
  sh "googlecode_upload.py -s \"YASnippet Release #{$version}\"" +
    " -p yasnippet -l \"Featured,Type-Package,OpSys-All\"" +
    " pkg/yasnippet-#{$version}.tar.bz2"
  sh "googlecode_upload.py -s \"YASnippet Bundle #{$version}\"" +
    " -p yasnippet -l \"Featured,Type-Package,OpSys-All\"" +
    " pkg/yasnippet-bundle-#{$version}.el.tgz"

end

desc "Generate document"
task :doc do
  docs = Dir['doc/*.rst']
  docs.each { |file|
    sh "doc/compile-doc.py #{file} #{file.sub(/rst$/, "html")}"
  }
end

task :default => :doc
