# 7.4: Searchable snapshots

In this lesson, you’ll learn how searchable snapshots can dramatically reduce your live data retention requirements while keeping your legacy data available for searches. You will also learn how to create and manage snapshots for disaster recovery.

In this lab, you will learn about snapshots by performing the following tasks:

    Create a shared file system snapshot repository
    Configure your index lifecycle policy to add searchable snapshots
    Define a snapshot lifecycle policy

## a) Within Kibana2, go to Stack Management > Snapshot and Restore.

You will register a new repository that satisfies the following requirements:

    The name of the repository is snap-repo
    The repository is a shared file system that maps to the /var/tmp/snapshots folder

Keep in mind that cluster2 already has path.repo set to /var/tmp/snapshots, which is a shared file system on all nodes.

## b) Open the Repositories tab, and click to Register a Repository.
## c) Name the repository snap-repo a select Shared file system as the type. Then, click Next.
## d) Set the File system location to /var/tmp/snapshots and click Register

Next you will edit metrics-custom-policy to enable Searchable snapshots are in the Cold phase using the snap-repo repository.

## e) Go to Index Lifecycle Policies
## f) Click to metrics-custom-policy to edit it.
## g) In the Cold phase section, toggle on the switch to enable Convert to fully-mounted index and set the Snapshot repository to snap-repo.
## h) Scroll to the bottom and click to Save policy.
## i) Go to Index Management.
## j) Open the Data Streams tab.
## k) Locate metrics-system.memory-labenv and click on the number of indices for the stream.
## l) You can visualize the different indices linked to the stream. Notice that the size is different depending on the phase they are in. The indices in the cold phase should have a much better ratio size/documents.
## m) Go to Snapshot and Restore.

Next you will create a new snapshot policy that satisfies the following requirements:

    The policy name is my-daily-snaps
    The snapshot name is <my-daily-{now/d}>
    The policy uses the snap-repo repository
    The policy takes a snapshot at 1:30 AM every day
    The policy retains a maximum of 3 snapshots

## n) Open the Policies tab and click to Create a policy.
## o) On the Logistics page, set the Policy name to my-daily-snaps, set the Snapshot name to <my-daily-{now/d}>, and click Next.
## p) On the Snapshot settings page, leave the default settings and click Next.
## q) On the Snapshot retention page, for Snapshots to retain set the Maximum count to 3 and click Next.
## r) Click Create policy.

TIP: You can also create the policy using the following command in Console

REQUEST

```
PUT _slm/policy/my-daily-snaps
{
  "name": "<my-daily-{now/d}>",
  "schedule": "0 30 1 * * ?",
  "repository": "snap-repo",
  "config": {},
  "retention": {
    "max_count": 3
  }
} 
```

RESPONSE

```
{
  "acknowledged": true
}
```

## s) Instead of waiting for the first snapshot, click the Run now arrow on the Policies tab. Then click to Run policy.
## t) Once the snapshot has run, open the Snapshots tab and examine the results to see that all of your indices have been backed up.

# Summary

In this lab, you learned about snapshots by performing the following tasks:

    Create a shared file system snapshot repository
    Configure your index lifecycle policy to add searchable snapshots
    Define a snapshot lifecycle policy

# Review

- When configuring a searchable snapshot in ILM, the Repo Name must first be set using the Snapshot and Restore API.
- Searchable snapshots doesn't need replica shards. The snapshot itself acts as the replica.
- You can take a snapshot of your entire cluster in a single REST request. You can also automate this process using SLM.
