# [fit] pgloader

# Migrate things into Postgres good

^ Hi, I'm Myles Braithwaite and tonight I'm going to be talking about pgloader a Postgres utility to loading data from files or migrating a whole database to Postgres.

---

# I'm not a
# [fit] DBA

![](photos/tobias-fischer-185901-unsplash.jpg)

^ Just so you know, I'm not a DBA.

---

# I'm a
# [fit] Programmer &
# [fit] Data Scientist

![](photos/jefferson-santos-450403-unsplash.jpg)
![](photos/rawpixel-656709-unsplash.jpg)

^ I'm a programmer and data scientist.

---

![](photos/GrantMatch.png)

^ I work for a company called GrantMatch. We make it easier for companies to manage their funding strategy using a large dataset of funding programs and historical grant approval to match our users with the best available grants for their business.

---

![](photos/heroku-wallpaper.png)

^ The application was being hosted on Digital Ocean, managed by ServerPilot, and deployed with DeployBot. I wanted to migrate to Heroku for simpler management of the application (as I'm the only technical person at the company).

---

![](photos/talia-cohen-30431-unsplash.jpg)

![](photos/aj-robbie-603166-unsplash.jpg)

^ Because Heroku has amazing Postgres support I wanted to migrate the MySQL database to Postgres to fit into their ecosystem better.

---

![](photos/fuyong-hua-658447-unsplash.jpg)

^ So the original title of this talk was going to be, 'How migrating a MySQL database to Postgres made me really sad'.

---

![](photos/fuyong-hua-658447-unsplash.jpg)

- [mysql-postgresql-converter](https://github.com/lanyrd/mysql-postgresql-converter)
-   [mysql2postgres](https://github.com/maxlapshin/mysql2postgres)
-   [pg_chameleon](https://pypi.org/project/pg_chameleon/)

^ I spent a year researching ways on how ot migrate the MySQL database to Postgres. But it seemed like all the projects that existed were outdated and finally gave up.

---

![](photos/adam-jang-260876-unsplash.jpg)

^ But then I found pgloader and all it took was a couple of commands to migrate the database.

---

![](photos/adam-jang-260876-unsplash.jpg)

```
brew install pgloader
```

or

```
apt install pgloader
```

^ First installation of pgloader is in most major package repositories.

---

![](photos/adam-jang-260876-unsplash.jpg)

```
pgloader
  mysql://127.0.0.1/grantmatch
  postgresql://127.0.0.1/grantmatch
```

^ Then just saying where say where the data is we want to load and what Postgres database we want to load it in.

---

![](photos/adam-jang-260876-unsplash.jpg)

```
                                 table name     errors       rows      bytes      total time
-------------------------------------------  ---------  ---------  ---------  --------------
                            fetch meta data          0        190                     0.240s
                             Create Schemas          0          0                     0.006s
                           Create SQL Types          0          0                     0.006s
                              Create tables          0         72                     0.177s
                             Set Table OIDs          0         36                     0.008s
-------------------------------------------  ---------  ---------  ---------  --------------
                          grantmatch.table           0     528034   634.0 MB        1m1.043s
-------------------------------------------  ---------  ---------  ---------  --------------
                    COPY Threads Completion          0          4                    45.583s
                             Create Indexes          0         97                    12.099s
                     Index Build Completion          0         97                     2.206s
                            Reset Sequences          0         35                     0.119s
                               Primary Keys          0         35                     0.038s
                        Create Foreign Keys          0         57                     0.997s
                            Create Triggers          0          0                     0.001s
                           Install Comments          0          0                     0.000s
-------------------------------------------  ---------  ---------  ---------  --------------
                          Total import time          ✓     528034   634.0 MB        1m1.043s
```

^ It send you back a report of how the data was migrated. and hopefully there were no errors. In my case there wasn't.

---

![](photos/george-pagan-iii-624417-unsplash.jpg)

^ In my case there wasn't. And because we were developing the software using an ORM it took little time to migrate the application.

---

![](photos/george-pagan-iii-624417-unsplash.jpg)

# Dimitri Fontaine's Continuous Migration

1.  Setup your target PostgreSQL architecture
2.  Fork a Continuous Integration environment that uses PostgreSQL
3.  Migrate the data over and over again every night, from your current production RDBMS
4.  As soon as the CI is all green using PostgreSQL, schedule the D-day
5.  Migrate without any suprises… and enjoy!

<https://pgloader.io/white-paper/>

^ I followed the author of pgloader, Dimitri's guide to Continuous Migration.

---

![](photos/jeroen-wehkamp-717284-unsplash.jpg)

^ There is another way I use pgloader.

---

# [fit] Data
# [fit] Science

![](photos/jeroen-wehkamp-717284-unsplash.jpg)

^ Data Science

---

![](photos/markus-spiske-109588-unsplash.jpg)

^ In data science there is one format:

---

# [fit] CSV

![](photos/markus-spiske-109588-unsplash.jpg)

^ CSV and maybe some Excel files. CSV is great but when the files get large there sometimes is corruption worries.

---

```
LOAD CSV
  FROM locations.csv
  HAVING FIELDS
    (
      type_code, type_desc, code, description, name, address, phone, notes,
      latitude, longitude
    )

  INTO postgresql:///to_open_data
  TARGET TABLE locations
  TARGET COLUMNS
    (
      type_code, type_desc, code, description, name, address, phone, notes,
      location point using (format nil "(~a,~a)" longitude latitude)
    )

  WITH truncate,
    disable triggers,
    skip header = 1,
    fields terminated by ','

  BEFORE LOAD DO
    $$ DROP TABLE IF EXISTS locations; $$,
    $$ CREATE TABLE locations (
      type_code TEXT, type_desc TEXT, code TEXT, description TEXT, name TEXT,
      address TEXT, phone TEXT, location POINT
    );
    $$;
```

<https://git.io/fNaue>

---

![inline fill](photos/jupyter-notebook.png)

<https://git.io/fNauY>

---

# [fit] Myles Braithwaite

-   🌎 [mylesb.ca](https://mylesb.ca/)
-   📬 [me@mylesb.ca](mailto:me@mylesb.ca)
-   🐦 [twitter.com/mylesb](https://twitter.com/mylesb)
-   👔 [linkedin.com/in/mylesbraithwaite](https://linkedin.com/in/mylesbraithwaite)

---

# Photo Credits

[Tobias Fischer](https://unsplash.com/photos/PkbZahEG2Ng), [Jefferson Santos](https://unsplash.com/photos/9SoCnyQmkzI), [rawpixel](https://unsplash.com/photos/8dHheu02i8s), [Talia Cohen](https://unsplash.com/photos/i5FsBOLsB50), [AJ Robbie](https://unsplash.com/photos/BuQ1RZckYW4), [FuYong Hua](https://unsplash.com/photos/tEMU4lzAL0w), [Adam Jang](https://unsplash.com/photos/8pOTAtyd_Mc), [George Pagan III](https://unsplash.com/photos/PsifAN6_z-k), [Jeroen Wehkamp](https://unsplash.com/photos/UzRUVbXVxVM), and [Markus Spiske](https://unsplash.com/photos/xekxE_VR0Ec).
