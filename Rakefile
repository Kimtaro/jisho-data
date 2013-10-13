DATA_DIR = File.join(File.dirname(__FILE__), 'data') + '/'

task :default => ['data:all']

namespace :git do
  desc "Git pull"
  task :pull do
    sh "git pull"
  end

  desc "Git push"
  task :push do
    sh "git push"
  end
  
  desc "Git add ."
  task :add, :path do |t, args|
    sh "git add #{args[:path]}"
  end
  
  desc "Git commit"
  task :commit, :message do |t, args|
    sh "git commit -m '#{args[:message]}'"
  end
end

namespace :data do
  task :ensure_data_directory do
    Dir.mkdir(DATA_DIR) unless File.exists?(DATA_DIR)
  end

  desc "Download and update all data"
  task :all do
    Rake::Task['git:pull'].invoke
    Rake::Task['data:update:jmdict'].invoke
    Rake::Task['git:push'].invoke
  end
  
  namespace :update do
    desc "Update JMdict"
    task :jmdict do
      Rake::Task['data:dl:jmdict'].invoke
      Rake::Task['git:add'].invoke(DATA_DIR)
      Rake::Task['git:commit'].invoke("JMdict #{Time.now}")
    end
  end

  namespace :dl do
    desc "Download JMdict"
    task :jmdict => [:ensure_data_directory] do
      dl_and_gunzip('http://ftp.monash.edu.au/pub/nihongo/JMdict.gz')
    end

    def dl(url)
      filename = url.split('/')[-1]
      target_path = DATA_DIR + filename
      sh "rm #{target_path}" if File.exists?(target_path)
      sh "wget -O #{target_path} #{url}"
      target_path
    end

    def gunzip(path)
      sh "gunzip -f #{path}"
    end

    def bunzip2(path)
      sh "bunzip2 -f #{path}"
    end

    def dl_and_gunzip(url)
      path = dl(url)
      gunzip(path)
    end

    def dl_and_bunzip2(url)
      path = dl(url)
      bunzip2(path)
    end
  end
end
