---
id: njvhqqpga0iuqwog2n5f67r
title: Scheduler
desc: "Setting Up Supervisor and Cron for Laravel Queue in Production"
updated: 1672742507710
created: 1672741922948
---

> [Medium](https://gblend.medium.com/setting-up-supervisor-and-cron-for-laravel-queue-in-production-163a89603355) -- Resource Link

Running the scheduler

When using Laravel’s scheduler, we only need to add a single cron configuration entry to our server that runs the schedule:run command every minute or any other schedule expression specified. To add the cron entry to your server, open cron entry file by running `crontab -e` in your linux terminal.

```shell
crontab -e
```

Add this entry to the file:

```conf
* * * * * cd /path-to-your-project && php artisan schedule:run >> /dev/null 2>&1
```

Examine the Status of the Cron Service

In other to check if crontab is working by checking it status, you need to run the command below in your terminal:

```shell
systemctl status cron
```

Running the command above along with the status flag will check the status of the cron service. If the status is ‘Active (Running)’ then it will be confirmed that crontab is working well or otherwise if not.

```shell
sudo service cron start
```
