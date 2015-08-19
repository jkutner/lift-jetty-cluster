# lift-jetty-cluster-aws
Sample Lift project that runs embedded Jetty in a cluster on AWS

MySQL setup to run locally:

```text
mysql> create database lift_sessions;
Query OK, 1 row affected (0.02 sec)

mysql> create user 'jetty'@'localhost' identified by 'lift-rocks';
Query OK, 0 rows affected (0.02 sec)

mysql> grant all on lift_sessions.* TO 'jetty'@'localhost';
Query OK, 0 rows affected (0.00 sec)
```

You may use your own database name, user name, or password.
They can all be configured in `src/main/resources/props/default.props`.

Build the application by running `sbt stage`.
This will produce a runnable script in `target/universal/stage/bin`.
Run the script.

Once the server boots, you can see jetty built two tables:
```text
mysql> use lift_sessions;
Database changed
mysql> show tables;
+-------------------------+
| Tables_in_lift_sessions |
+-------------------------+
| jettysessionids         |
| jettysessions           |
+-------------------------+
2 rows in set (0.00 sec)
```

Open your browser to `http://localhost:8080`.
Now you will see entries in the two tables.

```text
mysql> select * from jettysessionids;
+------------------------------------+
| id                                 |
+------------------------------------+
| B4LLKLNHFDomk1vhk5d9ow2rklylmi0gju |
+------------------------------------+
1 row in set (0.00 sec)
```

This is the `JSESSIONID` cookie value set in your browser.
(See for yourself in Chrome by looking on the _Resources_ tab in the _Developer Tools_)
The first 10 digits were created with `net.liftweb.util.StringHelpers.randomString` in `bootstrap.liftweb.Start`.
It is important that each Lift server instance sets this uniquely.
A second server with this same configuration (or a second _run_ of the same server) will prefix all of the cookies with a different random string.
