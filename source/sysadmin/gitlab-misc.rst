GitLab: misc
############

Export existing repositories to gitlab
======================================

GitLab can create projects for you bare repos

* Copy bare repositories to /home/git/repositories
* Run ``bundle exec rake gitlab:import:repos RAILS_ENV=production``


Rebuild keys in gitolite (GitLab 4.2)
=====================================

Run, as the ``gitlab`` user, from ``/home/gitlab/gitlab``::

    bundle exec rake gitlab:gitolite:update_keys RAILS_ENV=production
    bundle exec rake gitlab:gitolite:update_repos RAILS_ENV=production


Run checks
==========

Run, as the ``gitlab`` user, from ``/home/gitlab/gitlab``::

    bundle exec rake gitlab:check RAILS_ENV=production


Fix: rake (gitlab 3.something)
==============================

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


Fix: postgres error on issues (gitlab 3.something)
==================================================

See https://gist.github.com/gitlabhq/gitlabhq/issues/1962

**Note:** I ended up giving up using PostgreSQL with GitLab, and installed
MySQL, as it seems the first one is not-so-well supported..

::

    sudo -u postgres psql -d gitlabhq_production

    CREATE CAST (integer AS text) WITH INOUT AS IMPLICIT;


