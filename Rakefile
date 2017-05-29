require_relative 'models/song_uploader'
require 'resque/tasks'
require 'data_mapper'
require 'pg'


desc "create db"
task :create_db do
  if ENV["RACK_ENV"] != "production"
    conn = PG.connect(dbname: 'postgres')
    conn.exec("CREATE DATABASE jfeliz")
    DataMapper.auto_migrate!
    puts "Created the jfeliz DB and auto-migrated"
  else
    puts "Skipped auto_migration - in production env"
  end
end

desc "Drop and create db then add test user"
task :reset_db do
 if ENV["RACK_ENV"] != "production"
    conn = PG.connect(dbname: 'postgres')
    conn.exec("DROP DATABASE jfeliz")
    puts "dropped the jfeliz DB"
    Rake::Task["create_db"].invoke
  else
    puts "Skipped"
  end 
end

namespace :resque do
  task :setup do
    require 'resque'
    ENV['QUEUE'] = '*'

    if ENV["RACK_ENV"] != 'production'
      Resque.redis = 'localhost:6379'
    else
      uri = URI.parse(ENV["REDISTOGO_URL"])
      Resque.redis = Redis.new(:host => uri.host, :port => uri.port, :password => uri.password)
    end
  end
end
