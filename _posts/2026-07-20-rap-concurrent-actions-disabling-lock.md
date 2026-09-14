---
title: "Concurrent Actions in RAP — Disabling Lock"
date: 2026-07-20 08:00:00 +0530
categories: [ABAP RAP]
tags: [rap, abap, actions, lock]
description: "Learn how to disable the RAP instance lock for a specific action using lock: none to allow concurrent action execution by multiple users."
---

Recently, we had a scenario in RAP where an action was available on the Object Page in display mode to schedule a background job.

The problem was that when two users tried to trigger the action at the same time on the same instance, the first user got the lock, and the second user was unable to execute the action.

In our case, the action was not updating any data, so locking the instance was not really needed. However, by default, RAP actions acquire a lock before execution.

To solve this, we disabled the lock for that specific action. After that, multiple users were able to schedule background jobs at the same time without any lock issues.

```abap
action ( lock: none ) ActionName;
```

![Instance locked error and lock none syntax](/assets/images/rap_action_lock_none.png)
