# PostgreSQL 설치 및 설정

## 준비

-	운영체제 : CentOS 7.4

## 설치

### Repository 추가

```bash
su -
rpm -Uvh https://yum.postgresql.org/10/redhat/rhel-7-x86_64/pgdg-centos10-10-2.noarch.rpm
```

### PostgreSQL 설치

```bash
yum install -y postgresql10-server postgresql10
```

### /tablespace/postgres 디렉터리 생성

```bash
$ mkdir -p /tablespace/postgres
```

### /tablespace/postgres 디렉터리 소유자 및 그룹 변경

```bash
$ chown -R /tablespace/postgres
```

### PostgreSQL DB 초기화

```bash
$ su - postgres
$ /usr/pgsql-10/bin/initdb -D /tablespace/postgres/pgsql/data
$ exit
```

-D : PostgreSQL 초기 설정 데이터 위치

> http://bestugi.tistory.com/17



## PostgreSQL 구동 & 종료

### postgresql-10.service 수정

```bash
$ vi /usr/lib/systemd/system/postgresql-10.service
```

```vim
Environment=PGDATA=/tablespace/postgres/pgsql/data/
```

```bash
$ systemctl daemon-reload
```

### PostgreSQL 구동

```bash
$ systemctl start postgresql-10 # 시작
$ systemctl status postgresql-10 # 상태확인
$ systemctl enable postgresql-10 # 부팅시 시작
```

### PostgreSQL 종료

```bash
$ systemctl stop postgresql-10
```



## 초기 설정

### 관리자 비밀번호 설정

```bash
$ su - postgres
$ psql

postgres=# \password postgres
Enter new password:[암호]
Enter it again:[암호 재입력]
postgres=# \q
```

### PostgreSQL conf 파일 수정

-	로컬, 원격 암호 접속 허용
-	포트 번호 10090 설정

> postgres.conf

```bash
$ vi /tablespace/postgres/pgsql/data/postgres.conf

#listen_addresses = 'localhost'         # what IP address(es) to listen on;
listen_addresses = '*'          # what IP address(es) to listen on;
                                        # comma-separated list of addresses;
                                        # defaults to 'localhost'; use '*' for all
                                        # (change requires restart)
#port = 5432                            # (change requires restart)
port = 10090
```

> pg_hba.conf

```bash
$ vi /tablespace/postgres/pgsql/data/pg_hba.conf
```

```vim
# TYPE  DATABASE        USER            ADDRESS                 METHOD

# "local" is for Unix domain socket connections only
local   all             all                                     md5
# IPv4 local connections:
host    all             all             0.0.0.0/0               md5
```

-	TYPE (접근자의 접근위치와 통신의 암호화 관련 설정)

  -	local : Unix Domain Socket을 통한 접속
  -	hostssl : ssl인증서를 통한 암호화 통신만 지원하며 localhost, 127.0.0.1식의 즉 TCP/IP접속에 해당
  -	hostnossl : ssl접속은 불가능하며, TCP/IP통신을 지원

-	DATABASE

  -	all : 모든 데이터베이스 접근
  -	DB 명 : 특정한 디비에 대한 접속을 제한할 수 있으며 ,(콤마)로 여러 개의 DB로 접근 제어가 가능.
  -	@DBlist file : DB가 많다면 dblist파일로 따로 만들어 PGDATA로 설정한 폴더의 안에 넣어 관리

-	USER

  -	all : 모든 유저
  -	user 명 : 계정설정으로 ,(콤마)구분으로 할 수 있음
  -	@파일명 : 파일로 처리 가능

-	ADDRESS (CIDR 방식)

  -	IPv4 CIDR구분으로 해당 C Class에 대해 모두 접근처리를 할 경우 : xxx.xxx.xxx.0/24
  -	해당 IP에 대한 접근처리를 할 경우 : xxx.xxx.xxx.xxx/32

-	METHOD (계정의 패스워드에 대한 서버로의 전송을 어떻게 할 것인가를 정하는 것)

  -	trust : 패스워드 없이 접근 가능
  -	reject : 거부
  -	md5 : 패스워드를 md5로 암호화해서 전송
  -	password : text로 패스워드를 전송하는 것.
  -	krb4, krb5 : KerberOS V4, 5를 지원.
  -	ident : 접속 ClientOS User이름을 확인하는 방법
  -	pam : PAM(Pluggable Authentication Modules)서비스를 사용한 인증

설정 후 재시작

```bash
$ systemctl stop postgresql-10
$ systemctl start postgresql-10
```

### firewall - 외부 접속 포트 허용

```bash
$ firewall-cmd --permanent --zone=public --add-port=10090/tcp

$ systemctl stop firewalld # 재시작
$ systemctl start firewalld

$ firewall-cmd --list-all-zones # 적용 확인
```

### 그 외 postgres.conf 설정

#### Connection And Authentication

```vim
max_connections = 1000
```

-	DB 서버에 대한 최대 동시 연결 수 설정

#### Resourece Usage (except WAL)

```vim
shared_buffers = 32GB
```

-	DB 서버가 공유 메모리 버퍼에 사용하는 메모리양 설정 (최소 128 kb).
-	메모리가 1GB 이상의 서버라면 시스템 메모리의 25%가 적당. PostgreSQL은 운영 체제 캐시에 의존 하기 때문에 지나치게 많이 할당하면 오히려 성능이 하락할 수 있음.

```vim
temp_buffers = 10MB
```

-	각 DB 세션에서 사용하는 임시 버퍼의 최대
-	임시 테이블에 대한 엑세스에만 사용되는 세션 버퍼
-	필요에 따라 최대 temp_buffers가 지정한 한계까지 할당

```vim
work_mem = 1GB
```

-	임시 디스크 파일에 쓰기 전에 내부 정렬 작업 및 해시 테이블에서 사용할 메모리양 설정
-	복잡한 쿼리일 수록, 여러 세션이 동시에 작업할 수 있기 때문에 사용된 전체 메모리는 work_mem의 몇 배가 될 수 있음

```vim
maintenance_work_mem = 8GB
```

-	create index 혹은 alter table add 등 유지 보수 작업에서 사용할 최대 메모리 양 설정
-	work_mem 값보다 크게 설정하여 안전하게 운용
-	데이터 제거 및 DB 덤프 복원 성능 향상

```vim
max_worker_processes = 10
```

-	백그라운드 프로세스의 최대 수

```vim
max_parallel_workers = 8
```

-	병렬 작업을 지원할 수 있는 최대 프로세스 수
-	max_worker_processes 보다 높게 설정해도 해당 설정 프로세스 풀에서 가져온 것이므로 아무런 영향을 미치지 않음
-	이 값을 변경할때 max_parallel_maintenance_workers 및 max_parallel_workers_per_gather 값도 조정

#### Write Ahead Log

```vim
synchronous_commit = off
```

-	쿼리가 클라이언트에 success 메시지를 반환하기 전에 WAL 레코드가 디스크에 기록되는 동안 트랜잭션 커밋이 대기할지 여부
-	지연 시간이 최대 wal_writer_delay 값의 3배
-	off 로 해놓더라도 DB 상태는 트랜잭션이 정상 중단된 경우와 같기 때문에 성능이 중요하다면 off를 권장

```vim
wal_buffers = 64MB
```

-	디스크에 아직 기록되지 않은 WAL 데이터에 사용되는 공유 메모리 양
-	값이 클 수록, 클라이언트가 한 번에 커밋하는 사용량이 많은 서버에서 쓰기 성능을 향상시킴

```vim
wal_writer_delay = 200ms
```

-	WAL 작성기가 WAL을 플러시하는 빈도
-	플러시를 한후 wal_writer_delay 값 동안 대기

```vim
checkpoint_timeout = 5min
```

-	자동 WAL 체크 포인트 간 최대 시간(초)

#### Replication

```vim
effective_cache_size = 32GB
```

-	단일 쿼리에서 사용할 수 있는 디스크 캐시의 유효 크기
-	값이 높을수록 인덱스 스캔이 더 많이 사용됨

#### Query Tuning

```vim
default_statistics_target = 100
```

-	값이 클수록 analyze를 수행하는 시간은 늘지만 예상 값은 향상 됨

```vim
constraint_exclusion = on
```

-	테이블 제약 조건 사용을 제어하여 쿼리를 최적화함
-	on : 모든 테이블 제약 조건 검사

#### Error Reporting And Logging

```vim
log_destination = 'stderr'
```

-	서버 메시지를 stderr에 기록`vim
  logging_collector = on
  `
-	stderr로 전송 된 로그를 파일로 리다이렉션

```vim
log_directory = 'log'
```

-	로그 파일 디렉토리 생성

```vim
log_filename = 'postgresql-%a.log'
```

-	로그 파일 이름 설정

```vim
log_truncate_on_rotation = on
```

-	설정한 시간만큼 파일에 추가됨.
-	지나면 새로운 로그 파일 생성

```vim
log_rotation_age = 1d
```

-	로그 파일의 로테이션 기간

```vim
log_rotation_size = 0
```

-	로그 파일 최대 사이즈. 크기를 기반으로 안두려면 0으로 지정

```vim
log_line_prefix = '%m [%p]'
```

-	로그 라인 시작부분 문자열

> https://www.postgresql.org/docs/10/index.html



## DB 만들기 예제

### USER 생성

```
postgres=# create user testuser password 'testpw';
CREATE ROLE
postgres=# alter user testuser with superuser;
ALTER ROLE
```

### Table Spcae 생성

테이블 스페이스가 될 디렉터리 생성~~~bash $ mkdir /tblsp $ chown -R postgres:postgres /tblsp~~~

PostgreSQL DB에서 tablespace 위치와 owner를 지정

```
postgres=# create tablespace tblsp location '/tblsp';
CREATE TABLESPACE
postgres=# alter tablespace tblsp owner to testuser ;
ALTER TABLESPACE

postgres=# \db
      테이블스페이스 목록
    이름    |  소유주  |  위치
------------+----------+--------
 pg_default | postgres |
 pg_global  | postgres |
 tblsp      | testuser | /tblsp
(3개 행)
```

### DB 생성

```
postgres=# create database testdb owner testuser;
```

### DB 접근 권한 주기

```
postgres=# grant ALL privileges on database testdb to testuser ;
postgres=# \l

데이터베이스 목록
이름    |  소유주  | 인코딩 |   Collate   |    Ctype    |      액세스 권한
-----------+----------+--------+-------------+-------------+-----------------------
postgres  | postgres | UTF8   | ko_KR.UTF-8 | ko_KR.UTF-8 |
template0 | postgres | UTF8   | ko_KR.UTF-8 | ko_KR.UTF-8 | =c/postgres          +
|          |        |             |             | postgres=CTc/postgres
template1 | postgres | UTF8   | ko_KR.UTF-8 | ko_KR.UTF-8 | =c/postgres          +
|          |        |             |             | postgres=CTc/postgres
testdb    | testuser   | UTF8   | ko_KR.UTF-8 | ko_KR.UTF-8 | =Tc/testuser           +
|          |        |             |             | testuser=CTc/testuser
(4개 행)
```

### Table Spcae 지정하기

```
postgres=# alter database testdb set tablespace tblsp;
ALTER DATABASE
```



## Time Scale 설치

> 출처: https://docs.timescale.com/v1.0/main

### RPM 다운로드 및 설치

```bash
wget https://timescalereleases.blob.core.windows.net/rpm/timescaledb-1.0.0-postgresql-10-0.x86_64.rpm
sudo yum install <rpm file>
```

### postgresql.conf 수정

PostgreSQL이 timescale을 사용할 수 있도록 아래와 같이 수정
`bash $ vi /tablespace/postgres/pgsql/data/postgresql.conf`

```vim 
#shared_preload_libraries = '' 
# (change requires restart) shared_preload_libraries = 'timescaledb' 
# (change requires restart)
```

### Timescale DB 생성

사용하고자 하는 DB에 접근 후 TimeScaleDB를 생성한다.

```sql
\c testdb;

create extension if not exists timescaledb cascade;
```

성공시 아래와 같이 출력된다.

```
WELCOME TO
 _____ _                               _     ____________
|_   _(_)                             | |    |  _  \ ___ \
  | |  _ _ __ ___   ___  ___  ___ __ _| | ___| | | | |_/ /
  | | | |  _ ` _ \ / _ \/ __|/ __/ _` | |/ _ \ | | | ___ \
  | | | | | | | | |  __/\__ \ (_| (_| | |  __/ |/ /| |_/ /
  |_| |_|_| |_| |_|\___||___/\___\__,_|_|\___|___/ \____/
               Running version 1.0.0
For more information on TimescaleDB, please visit the following links:

 1. Getting started: https://docs.timescale.com/getting-started
 2. API reference documentation: https://docs.timescale.com/api
 3. How TimescaleDB is designed: https://docs.timescale.com/introduction/architecture

Note: TimescaleDB collects anonymous reports to better understand and assist our users.
For more information and how to disable, please see our docs https://docs.timescaledb.com/using-timescaledb/telemetry.

CREATE EXTENSION
testdb=#
```

### HyperText 생성

TABLE : readings, device_info

```sql
#select create_hypertable('테이블 명','시계열 변수', 옵션..);
#ex)
select create_hypertable('readings', 'time', chunk_time_interval => interval '1 day');
```

### 실행 예제

Timescale DB는 여러 함수를 제공하는데 그 중 time_bucket() 함수를 활용한 select 문이다.
time_bucket은 시계열 변수를 가져와 지정한 특정 시간 단위로 그룹화 할 수 있다.

```sql
SELECT time_bucket('1 hour', time) as "hour",
min(battery_level) min_battery_level,
max(battery_level) max_battery_level
FROM readings r
WHERE r.device_id IN (
    SELECT DISTINCT device_id FROM device_info
    WHERE model = 'pinto' OR model = 'focus'
) GROUP BY "hour" ORDER BY "hour" ASC LIMIT 12;
```

결과

```
hour          | min_battery_level | max_battery_level
------------------------+-------------------+-------------------
2016-11-15 21:00:00+09 |                20 |                99
2016-11-15 22:00:00+09 |                12 |                98
2016-11-15 23:00:00+09 |                 8 |                97
2016-11-16 00:00:00+09 |                 6 |               100
2016-11-16 01:00:00+09 |                 6 |               100
2016-11-16 02:00:00+09 |                 6 |               100
2016-11-16 03:00:00+09 |                 6 |               100
2016-11-16 04:00:00+09 |                 6 |               100
2016-11-16 05:00:00+09 |                 6 |               100
2016-11-16 06:00:00+09 |                 6 |               100
2016-11-16 07:00:00+09 |                 6 |               100
2016-11-16 08:00:00+09 |                 6 |               100
(12 rows)
```

