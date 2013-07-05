# Structure and behavior of rake task

* 'Private' rake tasks should have no description.
* Delegate calls to external classes
* These classes should not write output to stdout or stderr
* These classes should throw exceptions
* Wrap the call in a begin rescue block
* Messages should be prefixed with a timestamp
* Caught exceptions can be enriched with more text, but should be output to stderr
* When rake task fails, exit it with 1


## example

    task :private_task do
      input_root_path = (input_path = ENV['INPUT_PATH']) && Pathname.new(input_path)
      output_root_path = (output_path = ENV['OUTPUT_PATH']) && Pathname.new(output_path)

      begin
        SomeClass.run(input_root_path, output_root_path)
      rescue => e
        msg = []
        msg << "[ERROR]: #{e.message}"
        msg << "Usage: rake #{args.task_name} INPUT_PATH=/path/to/input/folder OUTPUT_PATH=/path/to/output/folder"
        $stderr.puts msg
        exit 1
      end
    end

    desc <<-DESCRIPTION
      Put the description of the task here
    DESCRIPTION
    task :public_task do
      s = Time.now
      puts "[%s]: Start a public task" % s
      Rake::Task['private_task'].invoke
      Rake::Task['other_private_task'].invoke
      e = Time.now
      puts "[%s]: Public task finished (%.2fs)" % [e, (e - s)]
    end
