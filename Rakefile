require 'yaml'
require 'fileutils'

desc "Prepare the package"
task :prepare, [:version] do |task,args|
  config = YAML.load_file('config.yml')

  sh "echo #{args.version} > VERSION"

  # prepare directories
  Dir.mkdir 'source' unless Dir.exists?("source")
  FileUtils.mkdir_p 'workdir/etc/as-stats' unless Dir.exists?("workdir/etc/as-stats")
  FileUtils.mkdir_p 'workdir/usr/local/bin' unless Dir.exists?("workdir/usr/local/bin")
  FileUtils.mkdir_p 'workdir/var/www/as-stats' unless Dir.exists?("workdir/var/www/as-stats")
  FileUtils.mkdir_p 'workdir/usr/share/doc/as-stats' unless Dir.exists?("workdir/usr/share/doc/as-stats")
  Dir.mkdir 'packages' unless Dir.exists?("packages")

  # download and extract as-stats
  Dir.chdir 'source'
  sh "curl -L #{config['package_url']}/as-stats-v#{args.version}.tar.gz | tar xzf -" unless Dir.exists?("as-stats-v#{args.version}")

  # copy files
  Dir.chdir '..'
  FileUtils.cp_r "source/as-stats-v#{args.version}/conf/.", "workdir/etc/as-stats"
  FileUtils.cp_r "source/as-stats-v#{args.version}/bin/.", "workdir/usr/local/bin"
  FileUtils.cp_r "source/as-stats-v#{args.version}/www/.", "workdir/var/www/as-stats"
  FileUtils.cp_r "source/as-stats-v#{args.version}/tools/", "workdir/usr/share/doc/as-stats"
  FileUtils.cp_r "source/as-stats-v#{args.version}/contrib/", "workdir/usr/share/doc/as-stats"
  FileUtils.cp "source/as-stats-v#{args.version}/README", "workdir/usr/share/doc/as-stats"

end

desc 'Build the package'
task :build do
  Dir.chdir 'workdir'
  sh 'fpm -s dir -t deb -n hubot --prefix /opt -v $(cat ../VERSION) -d nodejs --after-install ../postinst --before-install ../preinst hubot'
  Dir.chdir '..'
  sh 'cp workdir/hubot_$(cat VERSION)_amd64.deb packages/'

  sh 'fpm -s dir -t deb -n hubot-init --prefix / -v $(cat VERSION) -d hubot,upstart --package packages/hubot-init-$(cat VERSION)_amd64.deb etc'
end

desc "Cleanup prepare dirs"
task :clean do
  FileUtils.rm_r 'workdir' if File.exists? 'workdir'
  FileUtils.rm_r 'source' if File.exists? 'source'
  FileUtils.rm_r 'packages' if File.exists? 'packages'
  FileUtils.rm 'VERSION' if File.exists? 'VERSION'
end
