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
    status = %x{git status}
    unless status =~ /nothing to commit|no changes added to commit/
      sh "git commit -m '#{args[:message]}'"
    end
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

    Rake::Task['git:add'].reenable
    Rake::Task['git:commit'].reenable
    Rake::Task['git:push'].reenable

    Rake::Task['data:update:kanjidic2'].invoke
    Rake::Task['git:push'].invoke

    Rake::Task['git:add'].reenable
    Rake::Task['git:commit'].reenable
    Rake::Task['git:push'].reenable

    Rake::Task['data:update:radk'].invoke
    Rake::Task['git:push'].invoke

    Rake::Task['git:add'].reenable
    Rake::Task['git:commit'].reenable
    Rake::Task['git:push'].reenable

    Rake::Task['data:update:tanaka'].invoke
    Rake::Task['git:push'].invoke
  end

  namespace :update do
    desc "Update JMdict"
    task :jmdict do
      Rake::Task['data:dl:jmdict'].invoke
      Rake::Task['git:add'].invoke(DATA_DIR)
      Rake::Task['git:commit'].invoke("JMdict #{Time.now}")
    end

    desc "Update Kanjidic2"
    task :kanjidic2 do
      Rake::Task['data:dl:kanjidic2'].invoke
      Rake::Task['git:add'].invoke(DATA_DIR)
      Rake::Task['git:commit'].invoke("Kanjidic2 #{Time.now}")
    end

    desc "Update Radkfile"
    task :radk do
      Rake::Task['data:dl:radk'].invoke
      Rake::Task['git:add'].invoke(DATA_DIR)
      Rake::Task['git:commit'].invoke("Radk #{Time.now}")
    end

    desc "Update Tanaka corpus"
    task :tanaka do
      Rake::Task['data:dl:tanaka'].invoke
      Rake::Task['git:add'].invoke(DATA_DIR)
      Rake::Task['git:commit'].invoke("Tanaka #{Time.now}")
    end
  end

  namespace :dl do
    desc "Download JMdict"
    task :jmdict => [:ensure_data_directory] do
      dl_and_gunzip('http://ftp.monash.edu.au/pub/nihongo/JMdict.gz')
    end

    desc "Download Kanjidic2"
    task :kanjidic2 => [:ensure_data_directory] do
      dl_and_gunzip('http://www.csse.monash.edu.au/~jwb/kanjidic2/kanjidic2.xml.gz')
    end

    desc "Download Radk"
    task :radk => [:ensure_data_directory] do
      dl_and_gunzip('ftp://ftp.monash.edu.au/pub/nihongo/radkfile.gz')
    end

    desc "Download Tanaka Corpus"
    task :tanaka => [:ensure_data_directory] do
      dl('http://tatoeba.org/files/downloads/wwwjdic.csv')
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
