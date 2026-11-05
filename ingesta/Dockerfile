# Imagen base: mismo Hadoop (3.2.1) y Java 8 que el NameNode/DataNode del compose.
# Trae el cliente `hdfs`/`hadoop` y el entrypoint que convierte las variables
# CORE_CONF_* en core-site.xml.
FROM bde2020/hadoop-base:2.0.0-hadoop3.2.1-java8

ENV SQOOP_HOME=/opt/sqoop \
    FLUME_HOME=/opt/flume \
    HADOOP_COMMON_HOME=/opt/hadoop-3.2.1 \
    HADOOP_HDFS_HOME=/opt/hadoop-3.2.1 \
    HADOOP_MAPRED_HOME=/opt/hadoop-3.2.1 \
    HOME=/home/iabd_admin
ENV PATH=$PATH:/opt/sqoop/bin:/opt/flume/bin

# Directorios del laboratorio (rutas del PDF)
RUN mkdir -p /opt/sqoop /opt/flume \
             /home/iabd_admin/flume_ingest \
             /home/iabd_admin/flume_configs

# --- Sqoop 1.4.7 + conector MySQL + commons-lang (necesario con Hadoop 3) ---
RUN curl -fsSL https://archive.apache.org/dist/sqoop/1.4.7/sqoop-1.4.7.bin__hadoop-2.6.0.tar.gz \
      | tar xz -C /opt/sqoop --strip-components=1 \
 && curl -fsSL -o /opt/sqoop/lib/mysql-connector-java-5.1.49.jar \
      https://repo1.maven.org/maven2/mysql/mysql-connector-java/5.1.49/mysql-connector-java-5.1.49.jar \
 && curl -fsSL -o /opt/sqoop/lib/commons-lang-2.6.jar \
      https://repo1.maven.org/maven2/commons-lang/commons-lang/2.6/commons-lang-2.6.jar

# --- Flume 1.11.0 ---
RUN curl -fsSL https://archive.apache.org/dist/flume/1.11.0/apache-flume-1.11.0-bin.tar.gz \
      | tar xz -C /opt/flume --strip-components=1

# Conflicto clásico Flume + Hadoop 3: sustituimos el Guava antiguo de Flume
# por el que usa Hadoop 3.2.1.
RUN rm -f /opt/flume/lib/guava-*.jar \
 && cp /opt/hadoop-3.2.1/share/hadoop/common/lib/guava-*.jar /opt/flume/lib/

# --- nano (opcional): el repo de Debian 9 está archivado; si falla, el build continúa ---
RUN (sed -i 's|deb.debian.org|archive.debian.org|g; s|security.debian.org/debian-security|archive.debian.org/debian-security|g; /stretch-updates/d' /etc/apt/sources.list \
 && apt-get -o Acquire::Check-Valid-Until=false update \
 && apt-get install -y --no-install-recommends nano \
 && rm -rf /var/lib/apt/lists/*) || echo "AVISO: nano no instalado (edita los ficheros desde el host)"

WORKDIR /home/iabd_admin

# El entrypoint de la imagen base genera la configuración de Hadoop y ejecuta el comando
CMD ["tail", "-f", "/dev/null"]
