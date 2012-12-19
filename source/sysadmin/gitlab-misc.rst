GitLab: misc
####

Fix: rake
====

File ``lib/tasks/resque.rake``:

.. code-block:: ruby

    require 'resque/tasks'

    # Fix Exception
    # ActiveRecord::StatementInvalid
    # Error
    # PGError: ERROR: prepared statement "a3" already exists
    task "resque:setup" => :environment do
      Resque.after_fork do |job|
        ActiveRecord::Base.establish_connection
      end
    end

    desc "Alias for resque:work (To run workers on Heroku)"
    task "jobs:work" => "resque:work"

See: https://github.com/gitlabhq/gitlabhq/pull/1655/files



Fix: postgres error on issues
====

See https://gist.github.com/gitlabhq/gitlabhq/issues/1962

::

    sudo -u postgres psql -d gitlabhq_production

    CREATE CAST (integer AS text) WITH INOUT AS IMPLICIT;


Export existing repositories to gitlab
====

GitLab can create projects for you bare repos

* Copy bare repositories to /home/git/repositories
* Run ``bundle exec rake gitlab:import:repos RAILS_ENV=production``
