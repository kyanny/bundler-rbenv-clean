# bundler & rbenv env study

以下のようなことをしたくて bundler と rbenv の環境変数について実験した

* bundle exec したプロセスから system() で外部プロセスを呼び出す
* 呼び出された外部プロセス側でサブディレクトリに移動し bundle install する
* サブディレクトリにある Gemfile を解釈して gem をインストールする
* サブディレクトリに .ruby-version があったらそのバージョンの Ruby で bundle install する

    $ bundle exec rake all_ruby
    $ bundle exec rake all_shell

## 考察

* Bundler のセットする環境変数 BUNDLE_GEMFILE RUBYOPT GEM_HOME を unset すれば、サブディレクトリに移動後 bundle install 実行時にそのディレクトリにある Gemfile を使う
* Bundler.with_clean_env は GEM_HOME をリセットしないが [code](https://github.com/carlhuda/bundler/blob/master/lib/bundler.rb#L210) このメソッドに渡したブロック内で system("bundle install") するのでも ok
* rbenv は RBENV_VERSION と RBENV_DIR という環境変数を export する
* RBENV_VERSION が、実行時にどの ruby ランタイムを使うかを制御するもの
* RBENV_DIR は .ruby-version とかをどのディレクトリを起点にしてロードするか判断するためのディレクトリパスが入ってる
* RBENV_VERSION が定義済みだとそのバージョンが使われる
* RBENV_VERSION を unset するだけだと RBENV_DIR が PWD にセットされてるので ./.ruby-version をロードしてしまう（サブディレクトリに移動してもそこの .ruby-version を読んでくれない）
* この、 RBENV_DIR から .ruby-version を読み込んでそのバージョン番号を RBENV_VERSION にセットして export する、というのを rbenv-exec がやっている
* なので両方とも環境変数を unset した上でサブディレクトリに移動し rbenv exec するとそこの .ruby-version を読んでくれて ruby ラインタイムのバージョンが切り替わる
* で最終的に、 ruby1.9 ランタイムで bundle exec して実行したプロセスから system() で、サブディレクトリの .ruby-version で指定した ruby を使って、サブディレクトリの Gemfile を使って bundle install できる
