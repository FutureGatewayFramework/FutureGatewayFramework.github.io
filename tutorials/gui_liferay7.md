---
layout: default
title: FG GUI examples with Liferay 7.x
exclude: true
---

TEST_CNT=fgtest_0.2
LIFERAY_IMAGE=esystemstech/liferay:7.2.0-ga1

cat > fgliferay.sql <<EOF
drop user if exists liferay;
drop database if exists lportal;
create user "liferay" IDENTIFIED BY "liferay";
CREATE DATABASE lportal;
grant all privileges on lportal.* TO "liferay"@"localhost" IDENTIFIED BY "liferay";
grant all privileges on lportal.* TO "liferay"@"%" IDENTIFIED BY "liferay";
flush privileges;
EOF
docker cp fgliferay.sql $TEST_CNT:/home/fgtest/fgliferay.sql
docker exec -ti -ti $TEST_CNT /bin/bash --login -c "asdbr < fgliferay.sql"

docker volume create fgliferay-data
docker run --name fgliferay\
           -h fgliferay\
           -d\
           -p 8080:8080\
           -e DB_HOST=$TEST_CNT\
           -e DB_SCHEMA=lportal\
           -e DB_USER=liferay\
           -e DB_PASSWORD=liferay\
           -v fgliferay-data:/opt/liferay/home/data\
           $LIFERAY_IMAGE

musql -u $DB_USER -p$DB_PASSWORD -h $DB_HOST $DB_SCHEMA



# INDIGO liferay plugins
curl -L https://github.com/indigo-dc/LiferayPlugIns/releases/download/2.2.1/LiferayPlugins-binary-2.2.1.tgz --output LiferayPlugins-binary-2.2.1.tgz

# GUI (Liferay)

docker run -d --name fgliferay -p 28080:8080 --link fgtest_0.2 -e JAVA_OPTS="-XX:NewSize=700m -XX:MaxNewSize=700m -Xms1024m -Xmx1024m -XX:MaxPermSize=128m -XX:+UseParNewGC -XX:+UseConcMarkSweepGC -XX:+CMSParallelRemarkEnabled -XX:SurvivorRatio=20 -XX:ParallelGCThreads=8" esystemstech/liferay:7.0.5-ga6

apt-get update
apt-get install -y --no-install-recommends\
            procps\
            curl\
            ca-certificates\
            sudo\
            git\
            mysql-client\
            mlocate\
            vim\
            gnupg\
            build-essential\
            locales\
            jq\
            nodejs

# Nodejs - https://hostadvice.com/how-to/how-to-install-node-js-on-ubuntu-18-04/
curl -sL https://deb.nodesource.com/setup_8.x | sudo bash -
apt-get install -y nodejs

npm install -g yo gulp jquery
npm install -g generator-liferay-theme@7.0.5

#SDK https://sourceforge.net/projects/lportal/files/Liferay%20IDE/

curl -L https://sourceforge.net/projects/lportal/files/Liferay%20IDE/3.7.1/LiferayProjectSDK-201910152009-linux-x64-installer.run/download --output LiferayProjectSDK-201910152009-linux-x64-installer.run

chmod +x LiferayProjectSDK-201910152009-linux-x64-installer.run

./LiferayProjectSDK-201910152009-linux-x64-installer.run

git clone https://github.com/liferay/liferay-blade-samples.git
cd liferay-blade-samples
./build.sh


blade samples -v 7.0 jquery-npm-portlet

blade init -v 7.0 liferay_workspace

#https://github.com/liferay/liferay-blade-samples

blade create -t npm-jquery-portlet testgui

blade create -t npm-jquery-portlet -v 7.0 testgui





