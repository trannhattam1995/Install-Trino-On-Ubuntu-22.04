# Steps to install Trino on Ubuntu

## Prepare

### Confirm Ubuntu Version

```bash
cat /etc/lsb-release
```

### Edit limits for the user that runs the Trino process

```bash
nano /etc/security/limits.conf
```

Add the following values (Recommend setting) and save the file.

```text
trino soft nofile 131072
trino hard nofile 131072
trino soft nproc 128000
trino hard nproc 128000
```

### Install Java runtime environment

Trino requires a 64-bit version of Java 21, with a minimum required version of 21.0.1.

```bash
sudo apt install openjdk-21-jdk
```

### Install Python

- version 2.6.x, 2.7.x, or 3.x
- required by the bin/launcher script only

## Install Trino

### Create Trino Folder

```
sudo mkdir /var/trino
```

### Download Trino

```bash
cd /var/trin
wget https://repo1.maven.org/maven2/io/trino/trino-server/438/trino-server-438.tar.gz -o .
tar -xvf trino-server-438.tar.gz
```

### Create data directory 

```bash
sudo mkdir ./data
```

### Configuring Trino

#### Create configuration directory

```bash
cd trino-server-438
sudo mkdir ./etc
```

#### Configuring Node Properties

```bash
nano etc/node.properties
```

Add the following lines and save the file.

```text
node.environment=production
node.id=ffffffff-ffff-ffff-ffff-ffffffffffff 
node.data-dir=/var/trino/data 
```

#### Configuring JVM

```bash
nano etc/jvm.config
```

Add the following lines and save the file.

```text
-server
-Xmx8G
-XX:InitialRAMPercentage=80
-XX:MaxRAMPercentage=80
-XX:G1HeapRegionSize=32M
-XX:+ExplicitGCInvokesConcurrent
-XX:+ExitOnOutOfMemoryError
-XX:+HeapDumpOnOutOfMemoryError
-XX:-OmitStackTraceInFastThrow
-XX:ReservedCodeCacheSize=512M
-XX:PerMethodRecompilationCutoff=10000
-XX:PerBytecodeRecompilationCutoff=10000
-Djdk.attach.allowAttachSelf=true
-Djdk.nio.maxCachedBufferSize=2000000
-Dfile.encoding=UTF-8
# Reduce starvation of threads by GClocker, recommend to set about the number of cpu cores (JDK-8192647)
-XX:+UnlockDiagnosticVMOptions
-XX:GCLockerRetryAllocationCount=32
```

#### Config properties (Testing purpose)

```bash
nano etc/config.properties
```

Add the following lines and save the file.

```text
coordinator=true
node-scheduler.include-coordinator=true
http-server.http.port=8080
discovery.uri=http://localhost:8080
```

#### Config Log levels

```bash
nano etc/log.properties
```

Add the following lines and save the file.

```text
io.trino=INFO
```

#### Config Catalog properties

##### Create Catalog mount fodler

```bash
mkdir etc/catalog
```

##### Example

```bash
nano etc/catalog/jmx.properties
```

Add the following line to mount the jmx connectors as the jmx catalog

```text
connector.name=jmx
```

## Running Trino

```bash
cd /var/trino/trino-server-438
bin/launcher start
```







## References

<https://trino.io/docs/current/installation/deployment.html>
