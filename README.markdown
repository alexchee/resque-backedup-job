resque-backedup-job
====================

Resque::Plugins::BackedupJob is Job extension that adds a redundant queue for jobs 
in case a worker gets killed before the job finishes.

This extensions works by storing the current job into another queue, QUEUENAME_in_use,
before the job processes, then removes the queue after it finishes.

If any workers shutdown before the job finishes, the job will still be in the
 _in_use queue. There are helper methods to reprocess the jobs and a hook to 
reinitialize the worker.

This is original designed for EC2 spot instances where 
machines could suddenly terminate and jobs would be incomplete.

To use RequeueWorker, add this line to your Job Class: 
    require 'resque/plugins/backedup_job'

And extend from Resque::Plugins::BackedupJob:
    class DeltaIndexer
		extend Resque::Plugins::BackedupJob
  	...
	end

To add some custom actions to run before the Job is recreated, override restart_job_action method:
	def restart_job_action(args)
		User.find args.first
	end
args is the same arguments that Worker.perform would get.

To recreate all the backed-up jobs before a certain time:
	DeltaIndexer.reprocess_missing_jobs_before(1.day.ago)


Resque-web interface extension coming soon.

Note on Patches/Pull Requests
-----------------------------
 
* Fork the project.
* Make your feature addition or bug fix.
* Add tests for it. This is important so I don't break it in a
  future version unintentionally.
* Commit, do not mess with rakefile, version, or history.
  (if you want to have your own version, that is fine but bump version in a commit by itself I can ignore when I pull)
* Send me a pull request. Bonus points for topic branches.

Copyright
---------

Copyright (c) 2011 Alex Chee. See LICENSE for details.