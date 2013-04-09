def with_env(env)
  original_env = ENV.to_hash
  ENV.update(env)
  yield
ensure
  ENV.replace(original_env)
end

def with_clean_rbenv
  with_env({'RBENV_VERSION' => nil, 'RBENV_DIR' => nil}) do
    yield
  end
end

desc 'Bundler env & rbenv env & chdir ALL control by Ruby code'
task :all_ruby do
  %w(r19 r20).each do |dir|
    Bundler.with_clean_env do
      with_clean_rbenv do
        Dir.chdir(dir) do
          sh 'rbenv exec bundle install --path=vendor/bundle'
        end
      end
    end
  end
end

desc 'Bundler env & rbenv env & chdir ALL controll by shell command'
task :all_shell do
  %w(r19 r20).each do |dir|
    sh "unset BUNDLE_GEMFILE RUBYOPT GEM_HOME RBENV_VERSION RBENV_DIR; cd #{dir}; rbenv exec bundle install --path=vendor/bundle"
  end
end

task :parent_gem do
  sh 'bundle exec gem list redis'
end

task :clean do
  %w(r19 r20).each do |dir|
    Dir.chdir(dir) do
      system 'rm -rf Gemfile.lock'
      system 'rm -rf .bundle/'
      system 'rm -rf vendor/'
    end
  end
end

task :display do
  %w(r19 r20).each do |dir|
    Dir.chdir(dir) do
      sh 'tree -L 4'
      sh "find . -type d -name 'redis'"
    end
  end
end

[:all_ruby, :all_shell].each do |task|
  Rake::Task[task].enhance([:clean, :parent_gem]) do
    Rake::Task[:display].invoke
  end
end
