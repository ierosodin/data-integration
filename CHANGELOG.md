# Changelog

## 2.6.0 (2019-02-12)

- Python 3.8 compatibility (explicitly set process spawning method to 'fork')
- Fix open runs after browser reload
- Add workaround for system statistics on wsl1
- Speedup incremental insert into partitioned tables
- Show warning when graphviz is not installed

## 2.5.1 (2019-08-01)

- Include file_dependencies as variable for Copy Commands: This could handle cases in ETL pipeline, where the copy command shall be skipped if the sql_files stay the same.


## 2.5.0 (2019-07-07)

- Bug fix: make last modification timestamp of parallel file reading time zone aware (fixes `TypeError: can't compare offset-naive and offset-aware datetimes` error) 


## 2.4.0 - 2.4.2 (2019-07-04)

- Add travis integration and PyPi upload


## 2.3.0 (2019-07-04)

- Add parameter `csv_format` and `delimiter_char` to `Copy` and `CopyIncrementally` commands.


## 2.2.0 (2019-07-02)

- Changed all `TIMSTAMP` to `TIMSTAMPTZ` in the mara tables. You have to manually run the
  below migration commands as `make migrate-mara-db` won't pick up this change.

**required changes**
You need to manually convert the mara tables to `TIMESTAMPTZ`:

```SQL
-- Change the timezone to whatever your ETL process is running in
ALTER TABLE data_integration_run ALTER start_time TYPE timestamptz
  USING start_time AT TIME ZONE 'Europe/Berlin';
ALTER TABLE data_integration_run ALTER end_time TYPE timestamptz
  USING end_time AT TIME ZONE 'Europe/Berlin';
ALTER TABLE data_integration_processed_file ALTER last_modified_timestamp TYPE timestamptz
  USING last_modified_timestamp AT TIME ZONE 'Europe/Berlin';
ALTER TABLE data_integration_node_run ALTER start_time TYPE timestamptz
  USING start_time AT TIME ZONE 'Europe/Berlin';
ALTER TABLE data_integration_node_run ALTER end_time TYPE timestamptz
  USING end_time AT TIME ZONE 'Europe/Berlin';
ALTER TABLE data_integration_node_output ALTER timestamp TYPE timestamptz
  USING timestamp AT TIME ZONE 'Europe/Berlin';
ALTER TABLE data_integration_file_dependency ALTER timestamp TYPE timestamptz
  USING timestamp AT TIME ZONE 'Europe/Berlin';
ALTER TABLE data_integration_system_statistics ALTER timestamp TYPE timestamptz
  USING timestamp AT TIME ZONE 'Europe/Berlin';
```

## 2.1.0 (2019-05-15)

- Track and visualize also unfinished pipeline runs
- Speed up computation of node durations and node cost
- Improve error handling in launching of parallel tasks
- Improve run times visualization (better axis labels, independent tooltips) 
- Smaller ui improvements


## 2.0.0 - 2.0.1 (2019-04-12)

- Remove dependency_links from setup.py to regain compatibility with recent pip versions
- Change MARA_XXX variables to functions to delay importing of imports
- move some imports into the functions that use them in order to improve loading speed
- Add ability to mask passwords in `Command`s, so they cannot show up in the UI anymore
  or are not written to the database in saved Events (config
  `data_integration.config.password_masks()`). See the example in the original function
  how to not let passwords show up in the settings UI.
  ([gh #14](https://github.com/mara/data-integration/pull/14))

**required changes** 

- Update `mara-app` to `>=2.0.0`


## 1.4.0 - 1.4.7 (2018-09-15)

- Use postgresql 10 native partitioning for creating day_id partitions in ParallelReadFile
- Catch and display exceptions when creating html command documentation
- Add python ParallelRunFunction
- Add option to use explicit upsert on incremental load (explicit UPDATE + INSERT)
- Emit a proper NodeFinished event when the launching of a parallel task failed
- Add option truncate_partition to parallel tasks
- Fix bug in run_interactively cli command
- Make it possible to run the ExecuteSQL command outside of a pipeline via .run()
- Add args parameter to RunFunction command
- Show redundant node upstreams as dashed line in pipeline graphs
- Fix problems with too long bash commands by using multiple commands for partition generation in ParallelReadXXX tasks

**required changes**

- When using `ParallelReadFile` with parameter `partition_target_table_by_day_id=True`, then make sure the target table is natively partitioned by adding `PARTITION BY LIST (day_id)`.
 


## 1.3.0 (2018-07-17)

- Add possibility to continue running child nodes on error (new `Pipeline` parameters `continue_on_error` and `force_run_all_children`)
- Make dependency on requests explicit


## 1.2.0 (2018-06-01)

- Implement ReadMode ONLY_CHANGED that reads all new or modified files
- Show node links in run output only relative to current node (to save space)


## 1.1.0 (2018-05-23)

- Add slack notifications to "run_interactively" cli command
- Add parameter max_retries to class Task
- Fix typos in Readme
- Optimize imports


## 1.0.0 - 1.0.4 (2018-05-02)

- Move to Github
- Improve documentation
- Add ReadMode 'ONLY_LATEST'
- Add new command `ReadScriptOutput`
- Add slack bot configuration
- Fix url in slack event handler
