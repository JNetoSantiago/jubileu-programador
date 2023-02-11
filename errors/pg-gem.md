Installing pg 1.4.5 with native extensions
Gem::Ext::BuildError: ERROR: Failed to build gem native extension.

    current directory: /home/joaoneto/.asdf/installs/ruby/3.1.2/lib/ruby/gems/3.1.0/gems/pg-1.4.5/ext
/home/joaoneto/.asdf/installs/ruby/3.1.2/bin/ruby -I /home/joaoneto/.asdf/installs/ruby/3.1.2/lib/ruby/3.1.0 -r ./siteconf20230210-27246-h2jtdl.rb extconf.rb
Calling libpq with GVL unlocked
checking for pg_config... no
checking for libpq per pkg-config... no
Using libpq from 
checking for libpq-fe.h... no
Can't find the 'libpq-fe.h header
*****************************************************************************

Unable to find PostgreSQL client library.

Please install libpq or postgresql client package like so:
  sudo apt install libpq-dev
  sudo yum install postgresql-devel
  sudo zypper in postgresql-devel
  sudo pacman -S postgresql-libs

or try again with:
  gem install pg -- --with-pg-config=/path/to/pg_config

or set library paths manually with:
  gem install pg -- --with-pg-include=/path/to/libpq-fe.h/ --with-pg-lib=/path/to/libpq.so/

*** extconf.rb failed ***
Could not create Makefile due to some reason, probably lack of necessary
libraries and/or headers.  Check the mkmf.log file for more details.  You may
need configuration options.

Provided configuration options:
	--with-opt-dir
	--without-opt-dir
	--with-opt-include
	--without-opt-include=${opt-dir}/include
	--with-opt-lib
	--without-opt-lib=${opt-dir}/lib
	--with-make-prog
	--without-make-prog
	--srcdir=.
	--curdir
	--ruby=/home/joaoneto/.asdf/installs/ruby/3.1.2/bin/$(RUBY_BASE_NAME)
	--with-pg
	--without-pg
	--enable-gvl-unlock
	--disable-gvl-unlock
	--enable-windows-cross
	--disable-windows-cross
	--with-pg-config
	--without-pg-config
	--with-pg_config
	--without-pg_config
	--with-libpq-dir
	--without-libpq-dir
	--with-libpq-include
	--without-libpq-include=${libpq-dir}/include
	--with-libpq-lib
	--without-libpq-lib=${libpq-dir}/lib
	--with-libpq-config
	--without-libpq-config
	--with-pkg-config
	--without-pkg-config
	--with-pg-dir
	--without-pg-dir
	--with-pg-include
	--without-pg-include=${pg-dir}/include
	--with-pg-lib
	--without-pg-lib=${pg-dir}/lib

To see why this extension failed to compile, please check the mkmf.log which can be found here:

  /home/joaoneto/.asdf/installs/ruby/3.1.2/lib/ruby/gems/3.1.0/extensions/x86_64-linux/3.1.0/pg-1.4.5/mkmf.log

extconf failed, exit code 1

Gem files will remain installed in /home/joaoneto/.asdf/installs/ruby/3.1.2/lib/ruby/gems/3.1.0/gems/pg-1.4.5 for inspection.
Results logged to /home/joaoneto/.asdf/installs/ruby/3.1.2/lib/ruby/gems/3.1.0/extensions/x86_64-linux/3.1.0/pg-1.4.5/gem_make.out

  /home/joaoneto/.asdf/installs/ruby/3.1.2/lib/ruby/3.1.0/rubygems/ext/builder.rb:95:in `run'
  /home/joaoneto/.asdf/installs/ruby/3.1.2/lib/ruby/3.1.0/rubygems/ext/ext_conf_builder.rb:47:in `block in build'
  /home/joaoneto/.asdf/installs/ruby/3.1.2/lib/ruby/3.1.0/tempfile.rb:317:in `open'
  /home/joaoneto/.asdf/installs/ruby/3.1.2/lib/ruby/3.1.0/rubygems/ext/ext_conf_builder.rb:26:in `build'
  /home/joaoneto/.asdf/installs/ruby/3.1.2/lib/ruby/3.1.0/rubygems/ext/builder.rb:161:in `build_extension'
  /home/joaoneto/.asdf/installs/ruby/3.1.2/lib/ruby/3.1.0/rubygems/ext/builder.rb:195:in `block in build_extensions'
  /home/joaoneto/.asdf/installs/ruby/3.1.2/lib/ruby/3.1.0/rubygems/ext/builder.rb:192:in `each'
  /home/joaoneto/.asdf/installs/ruby/3.1.2/lib/ruby/3.1.0/rubygems/ext/builder.rb:192:in `build_extensions'
  /home/joaoneto/.asdf/installs/ruby/3.1.2/lib/ruby/3.1.0/rubygems/installer.rb:853:in `build_extensions'
  /home/joaoneto/.asdf/installs/ruby/3.1.2/lib/ruby/3.1.0/bundler/rubygems_gem_installer.rb:71:in `build_extensions'
  /home/joaoneto/.asdf/installs/ruby/3.1.2/lib/ruby/3.1.0/bundler/rubygems_gem_installer.rb:28:in `install'
  /home/joaoneto/.asdf/installs/ruby/3.1.2/lib/ruby/3.1.0/bundler/source/rubygems.rb:204:in `install'
  /home/joaoneto/.asdf/installs/ruby/3.1.2/lib/ruby/3.1.0/bundler/installer/gem_installer.rb:54:in `install'
  /home/joaoneto/.asdf/installs/ruby/3.1.2/lib/ruby/3.1.0/bundler/installer/gem_installer.rb:16:in `install_from_spec'
  /home/joaoneto/.asdf/installs/ruby/3.1.2/lib/ruby/3.1.0/bundler/installer/parallel_installer.rb:186:in `do_install'
  /home/joaoneto/.asdf/installs/ruby/3.1.2/lib/ruby/3.1.0/bundler/installer/parallel_installer.rb:177:in `block in worker_pool'
  /home/joaoneto/.asdf/installs/ruby/3.1.2/lib/ruby/3.1.0/bundler/worker.rb:62:in `apply_func'
  /home/joaoneto/.asdf/installs/ruby/3.1.2/lib/ruby/3.1.0/bundler/worker.rb:57:in `block in process_queue'
  /home/joaoneto/.asdf/installs/ruby/3.1.2/lib/ruby/3.1.0/bundler/worker.rb:54:in `loop'
  /home/joaoneto/.asdf/installs/ruby/3.1.2/lib/ruby/3.1.0/bundler/worker.rb:54:in `process_queue'
  /home/joaoneto/.asdf/installs/ruby/3.1.2/lib/ruby/3.1.0/bundler/worker.rb:91:in `block (2 levels) in create_threads'

An error occurred while installing pg (1.4.5), and Bundler cannot continue.

In Gemfile:
  pg
