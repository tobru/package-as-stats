require 'yaml'
require 'fileutils'

desc "Prepare the package"
task :prepare, [:version] do |task,args|
  config = YAML.load_file('config.yml')

  sh "echo #{args.version} > VERSION"

  # prepare directories
  Dir.mkdir 'source' unless Dir.exists?("source")
  FileUtils.mkdir_p 'workdir/etc/as-stats' unless Dir.exists?("workdir/etc/as-stats")
  FileUtils.mkdir_p 'workdir/etc/init' unless Dir.exists?("workdir/etc/init")
  FileUtils.mkdir_p 'workdir/etc/default' unless Dir.exists?("workdir/etc/default")
  FileUtils.mkdir_p 'workdir/usr/local/bin' unless Dir.exists?("workdir/usr/local/bin")
  FileUtils.mkdir_p 'workdir/var/www/as-stats' unless Dir.exists?("workdir/var/www/as-stats")
  FileUtils.mkdir_p 'workdir/var/lib/as-stats/rrd' unless Dir.exists?("workdir/var/lib/as-stats/rrd")
  FileUtils.mkdir_p 'workdir/usr/share/doc/as-stats' unless Dir.exists?("workdir/usr/share/doc/as-stats")
  FileUtils.mkdir_p 'workdir/usr/lib/perl5/Net' unless Dir.exists?("workdir/usr/lib/perl5/Net")
  Dir.mkdir 'packages' unless Dir.exists?("packages")

  # download and extract as-stats
  Dir.chdir 'source'
  sh "curl -L #{config['package_url']}/as-stats-v#{args.version}.tar.gz | tar xzf -" unless Dir.exists?("as-stats-v#{args.version}")
  sh "curl -L #{config['perl_net_sflow_src']} > sFlow.pm"

  # copy files
  Dir.chdir '..'
  FileUtils.cp_r "source/as-stats-v#{args.version}/conf/.", "workdir/etc/as-stats"
  FileUtils.cp_r "source/as-stats-v#{args.version}/bin/.", "workdir/usr/local/bin"
  FileUtils.cp_r "source/as-stats-v#{args.version}/www/.", "workdir/var/www/as-stats"
  FileUtils.cp_r "source/as-stats-v#{args.version}/tools/", "workdir/usr/share/doc/as-stats"
  FileUtils.cp_r "source/as-stats-v#{args.version}/contrib/", "workdir/usr/share/doc/as-stats"
  FileUtils.cp "source/as-stats-v#{args.version}/README", "workdir/usr/share/doc/as-stats"
  FileUtils.cp "source/sFlow.pm", "workdir/usr/lib/perl5/Net"
  FileUtils.cp "scripts/upstart-job", "workdir/etc/init/as-stats.conf"
  FileUtils.cp "scripts/defaults", "workdir/etc/default/as-stats"

end

desc 'Build the package'
task :build do
  config = YAML.load_file('config.yml')

  version = File.read('VERSION').strip
  version += "-#{ENV['BUILD_NUMBER']}" if ENV['BUILD_NUMBER']

  Dir.chdir 'workdir'
  sh "fpm \
-s dir \
-t deb \
-n #{config['package_name']} \
--prefix / \
-v #{version} \
-d #{config['package_deps']} \
--config-files #{config['package_config_files']} \
-a all \
--description \"#{config['package_description']}\" \
--url \"#{config['package_website']}\" \
."

  Dir.chdir '..'
  sh "cp workdir/as-stats_#{version}_all.deb packages/"

end

desc "Cleanup prepare dirs"
task :clean do
  FileUtils.rm_r 'workdir' if File.exists? 'workdir'
  FileUtils.rm_r 'source' if File.exists? 'source'
  FileUtils.rm_r 'packages' if File.exists? 'packages'
  FileUtils.rm 'VERSION' if File.exists? 'VERSION'
end
