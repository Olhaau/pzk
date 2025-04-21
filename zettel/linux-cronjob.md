---
created: 2025-04-21
summary: Automated, recurring task in Linux.
---

# linux cronjob

## content

### quickstart


define the job as .sh make it executable. Edit cronjobs using 

```bash
crontab -e
```

and e.g. add

```bash
*/5 * * * * ~/jobs/auto_push_pzk.sh > /dev/null 2>&1
```

to update pzk remote repo each 5 min.

## refs

### up

[[linux]]

### down

