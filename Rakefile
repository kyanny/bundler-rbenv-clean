desc 'unset'
task :unset do
  %w(r19 r20).each do |dir|
    Dir.chdir(dir) do
      sh('sh -c "unset BUNDLE_GEMFILE RUBYOPT GEM_HOME; bundle install --path=vendor/bundle"')
    end
  end
end

desc 'Bundler.with_clean_env'
task :b do
  %w(r19 r20).each do |dir|
    Dir.chdir(dir) do
      Bundler.with_clean_env do
        sh('bundle install --path=vendor/bundle')
      end
    end
  end
end

desc 'inspect'
task 'tree' do
  %w(r19 r20).each do |dir|
    Dir.chdir(dir) do
      sh("tree -L 1 vendor/")
    end
  end
end

desc 'cleanup'
task 'clean' do
  %w(r19 r20).each do |dir|
    Dir.chdir(dir) do
      sh("rm -rf Gemfile.lock")
      sh("rm -rf vendor/")
      sh("rm -rf .bundle/")
    end
  end
end
