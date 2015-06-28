# tips! slash trash!
# in progress

## if /boot/ is full, remove unneeded kernels
list lastest kernel with 'uname -r', print out others from dpkg, then skip second latest
sudo apt-get remove --purge $(dpkg -l 'linux-*' | sed '/^ii/!d;/'"$(uname -r | sed "s/\(.*\)-\([^0-9]\+\)/\1/")"'/d;s/^[^ ]* [^ ]* \([^ ]*\).*/\1/;/[0-9]/!d'|sed '$d')


## use unix date command to 
# give current date in filename
pg_dump foo | gzip -c > foo_$(date +%F).gz
# convert between date formats
# this examples converts to YYYY-MM-DD
date -d "January 1, 2015" +%Y-%m-%d
# diff two dates - convert both to seconds first TODO

## check IP
ifconfig -a | grep inet

## check power use in watts
sudo apt-add-repository ppa:colin-king/powermanagement
sudo apt-get update
sudo apt-get install powerstat

## collapse multiple consecutive newlines into single newlines with
cat foo | cat -s

## with nl, number even blank lines
echo -e "yo\n\ndog" | nl -ba

## use mapshaper to dissolve a shapefile
$ mapshaper --encoding latin1 --dissolve ID_0 gadm2.shp -o gadm2_adm0.shp

# make a csv from a list of fields in any order from a directory of csvs
# basically grep for field numbers to use in awk, write these fields to file named by their input file, then paste these fields together to stdout
$ fieldlist="LOC_ID TOWN_ID FULL_STR ADDR_NUM"; function num { echo "$header" | grep -E "\b$1\b" | awk '{print $1}' ;}; for i in *.csv; do header=$( head -n 1 $i | tr '\t' '\n' | nl ); rm -r /tmp/$( basename $i ) 2>/dev/null; mkdir /tmp/$( basename $i ) 2>/dev/null; c=1; for field in $fieldlist; do cat $i | sed '1d' | awk -F"\t" "{OFS=\"\t\"; print \$$(num $field)}" > /tmp/$( basename $i )/${c}; c=$(($c+1)); done; paste -d'\t' /tmp/$( basename $i )/*; rm -r /tmp/$( basename $i ) 2>/dev/null; done > assess.csv

#use GNU parallel to print columns of the same name from different files.
$ for i in *.csv; do cat $i | parallel --colsep '\t' --header : 'echo -e {LOC_ID}"\t"{TOWN_ID}"\t"{FULL_STR}"\t"{ADDR_NUM}' >> assess.csv; done

# calls fields by name using GNU parallel
cat foo | parallel --gnu --colsep '\t' --header : 'echo {field_name},{another_field}'

# gnu parallel --pipe and -N to determine number of records to process at a time
# note in this example that nl numbers up to 100, then starts over - processing 100 records at a time
cat foo | parallel --pipe -N100 'cat | nl'

# bit.ly's data hacks
# run_for.py - do the preceeding command for n seconds
cat huge_file.txt | run_for.py 1s | mawk '{print $1}'

# R make a ton of box plots on a single graph
counts <- read.csv('/tmp/counts.csv')
library(ggplot2)
jpeg('/tmp/del.jpg')
ggplot(counts, aes( factor(state_count),district_count ) ) + geom_boxplot() + scale_y_log10() + opts(title="Where Indian NGOs Operate:\nBox Plots of Districts by Count of States")
dev.off()

# how to take time delayed screenshots in ubuntu
gnome-screenshot --delay 5

# install OpenBLAS
# http://www.stat.cmu.edu/~nmv/2013/07/09/for-faster-r-use-openblas-instead-better-than-atlas-trivial-to-switch-to-on-ubuntu/
$ sudo apt-get install libopenblas-base
# ensure right BLAS in use (eg OpenBLAS or ATLAS)
$ sudo update-alternatives --config libblas.so.3gf
# ensure R uses selected BLAS - first run R
$ R
# then lsof on its process id
$ ps aux | grep exec/R | grep -v "grep" | awk '{print $2}' |\
parallel lsof -p {} | grep -iE 'blas|lapack'
# install SuppDist from source
$ R CMD INSTALL SuppDists_1.1-9.1.tar.gz
# run Simon Urbanek's benchmarking script
$ cat R-benchmark-25.R | time R --slave
# determine which BLAS is fastest with your architecture

## when you mess up /etc/sudoers
try pkexec to get root and fix sudoers
please edit sudoers with visudo!


## classic ubuntu postgres install w/ postgis
sudo apt-get install postgresql-9.3-postgis-2.1
createuser -s -d -r -l -E username
createdb dbname
db=dbname
echo "CREATE EXTENSION POSTGIS;"  | psql $db

# example cronjob to autocommit to git upon changes in a dir
# example uses openrefine
@reboot /home/openrefine/google-refine-2.5/refine -i 0.0.0.0 -p 8888 -m 8192M &
@reboot cd /home/openrefine/.local;\
inotifywait -r -m -e CLOSE_WRITE -e MOVE -e DELETE . --exclude ".*[.]git.*" --format \
"git add . && git add -u && while read file;\
do\
    git rm -r --cached $file;\
done < <( find . -path ./.git -prune -o -size +100M -type f );\
git commit -m 'autocommit on change' && git push -u origin master" |\
bash &


# how to use awk getline to run shell commands inside awk!
# get the md5sum of fields 1,2,4 and prepend it to record
$ cat foo.csv | mawk -F, '{OFS=",";cmd="echo "$1$2$4" | sha1sum | grep -oE \"[^-]+\" | sed \"s:\s*$::g\""; while ( (cmd | getline sha1sum) > 0);print sha1sum,$0; close (cmd)}'


# when /etc/hosts.allow blocks you from ssh-ing
# example /etc/hosts.allow

# service : URL : state
sshd : xxx.xxx.xxx.xxx : allow

# ogrinfo sql with dialect option for more SQL!
ogr2ogr -dialect SQLite -sql "select * from G2014_2013_2 where ADM0_NAME in('Bolivia','Chile','Colombia','Brazil','Ecuador','Peru','Suriname','Uruguay','Argentina','Venezuela','Paraguay','Guyana','French Guiana')" del.shp g2014_2013_2/G2014_2013_2.shp

# how to add a new SRS to spatialite
cat > newsrs.sql <<EOF
BEGIN;

INSERT into spatial_ref_sys (srid, auth_name, auth_srid, ref_sys_name, proj4text) values (900913,'EPSG',900913,'Google Maps Global Mercator','+proj=merc +a=6378137 +b=6378137 +lat_ts=0.0 +lon_0=0.0 +x_0=0.0 +y_0=0 +k=1.0 +units=m +nadgrids=@null +no_defs');

COMMIT;
EOF

cat newsrs.sql | spatialite db.sqlite

# in R, reshape table from wide to long format
d <- read.csv('RemittanceData_Inflows_Oct2013.csv', sep='     ')

o <- reshape(d, direction="long",varying=list(names(d)[2:45]),idvar="Country",timevar="Year",times=1970:2013)

write.csv(o, file='/tmp/del.csv')
#eg as seen in stackoverflow
#http://stackoverflow.com/questions/2185252/reshaping-data-frame-from-wide-to-long-format/2185525#2185525
# if you got string as factors when importing csv then . . . 
d <- read.csv('Deflators_base_2012.tsv',sep="\t",stringsAsFactors=FALSE)
o <- reshape(d,direction="long",varying=list(names(d)[2:55]),v.names="value",idvar="donor",timevar="year",times=1960:2013)
write.csv(o, file='/tmp/del.csv')

# xmllint, use xpath to return an element if it exists
# in this example, take iati xml and return the sector vocab for each activity, if exists
$ xml=./unitedstates/Moldova.xml; activity_count=$( xmllint --xpath "count(iati-activities/iati-activity)" $xml ); for activity in $( seq 1 "$activity_count" ); do sector_count=$( xmllint --xpath "count(iati-activities/iati-activity[$activity]/sector)" $xml ); if [[ $sector_count -gt 0 ]]; then sector_vocabulary=$( xmllint --xpath "string(iati-activities/iati-activity[$activity]/sector/@vocabulary)" $xml ); echo $sector_vocabulary; fi; done

# another xml parsing example with IATI
get list of iati activity IDs that mentions mangrove and have at least one location
$ a="'";keyword=mangrove; find_ext xml | parallel --gnu 'haskeyword=$( cat {} | grep -i '$keyword' ); if [[ -n $haskeyword ]]; then tmpdir=$(mktemp -d); pyxie=$( xmlstarlet pyx {} 2>/dev/null); cd $tmpdir; csplit <( echo "$pyxie") /\)iati-activity/ {*} 1>/dev/null; grep -r '$keyword' . | grep -oE "^[^:]*" | while read activity; do hasloc=$( cat $activity | sed -n "/(location/,/)location/p" | grep -vE "^\s*$" | wc -l ); if [[ $hasloc -gt 0 ]]; then cat $activity | sed -n "/(iati-identifier/,/)iati-identifier/p" | grep -vE "^\s*$" | sed -n "2p" | sed "s:^-::g"; fi done; cd - 1>/dev/null; rm -r $tmpdir; fi'

# denyhosts - 
#make TSV of all IPs caugth by deny hosts, by host that caught them
$ hostsdeny="elcano zhanghe tereshkova"; db=scratch; for i in "$hostsdeny"; do tmp=$(mktemp); echo "address" > $tmp; cat $i | grep -E "^\s*sshd" | sed 's:^sshd\: ::g' >> $tmp; csv2psql $tmp $i | psql $db; done; join_multi_w_singleField "$hostsdeny" address scratch|less

# vim
#vim from stdin - very good for csvlook!
$ cat foo | csvlook -t | vim -

# GRASS GIS automated topology fixes with v.clean
# import lines as line,boundary type - helps build poly ("area") in GRASS
v.in.ogr type=line,boundary dsn=just_lines.shp output=a -o --overwrite
# clean lines
type=line
v.clean --overwrite type=$type input=a output=b tool=break,rmdangle,rmsa,rmdupl thresh=0,3,0,0,0
v.clean --overwrite type=$type input=b output=c tool=prune,snap thresh=1.5,3


# basic R functions to examine and handle your data
names()
str()
summary()
head()
tail()
dim()
levels()
ls()
rm()
length()
class()
fix()

# ogr2ogr replacement for pgsql2shp
ogr2ogr del.shp PG:"dbname='scratch' host='localhost' port='5432' user='adecatur'" -sql "select * from prec1;"

# mac os x steps for postgreSQL
brew install postgres
brew tap homebrew/boneyard
brew services start postgres
#note: pg_hba.conf under
#/usr/local/var/postgres/

# simplest possible http servers - when you need one fast!
## python 
cd foo
python -m SimpleHTTPServer
## quark
# http://tools.suckless.org/
#example use:
chown -R www-data:www-data foo/
cd foo/
sudo quark -g www-data -u www-data -l -p 8081
## netcat webserver
# absurd API replacement - cat a file to netcat, awk/grep/etc from client
# start nc web server
while :; do cat foo.txt | sudo nc -l 80 > /tmp/nc_server.log; done
# now grep for a keyword from 
nc localhost 80 | grep foo


# in awk, use sprintf() to define a variable using printf
# in this example, multiply comms and disburs by 1m and format with 2 decimals
$ cat crs2014-06-16.csv | mawk -F'|' '{ OFS="|"; comms=sprintf("%.2f", $24*1000000); disburs=sprintf("%.4f", $25*1000000); if ($10 ~ /^Indonesia$/ && $1 >2004) print $1,$6,$10,comms,disburs }'|less

# restore a postgresql db from tar'd sql
# had to install contrib first b/c of unaccent function
$ sudo apt-get install postgresql-contrib-9.1
# now run pg_restore on tar'd sql - don't try to untar!
$ db=example_db_w_accents; dropdb $db; createdb $db; pg_restore -d $db db_w_accents_dump_2014-09-16.sql

# bash: echo vs printf
# printf is more portable
# echo without \c produces a trailing newline
# note that these produce the same output
$ printf "abc" | md5sum
$ echo -e "abc\c" | md5sum
$ echo "select md5('abc')" | psql db
# but that echo alone produces different output
$ echo "abc" | md5sum
# this is because echo normally produces a trailing newline!

# compare grep, LANG=C grep, tre-agrep, and agrep for speed
# run each 50 times with perf, compare output mean times
# beware - the outputs are different.  these are not the same tasks exactly
# with grep
$ perf stat -r 50 bash -c 'grep "agrep" * >/dev/null 2>&1'
# 0.008986451 seconds time elapsed
# with LANG=C grep (sometimes helps!) - this one is the fastest in this case
$ perf stat -r 50 bash -c 'LANG=C grep "agrep" * >/dev/null 2>&1'
# 0.007076173 seconds time elapsed
# with agrep
$ perf stat -r 50 bash -c './agrep "agrep" * >/dev/null 2>&1'
# 0.007457215 seconds time elapsed
# with tre-agrep
$ perf stat -r 50 bash -c 'tre-agrep "agrep" * >/dev/null 2>&1'
# 0.047314294 seconds time elapsed
shockingly, agrep is actually faster than regular grep.
and ~6x faster than tre-agrep - for this particular task.


# bash redirect stdin and stderr to null device
# in this example, use perf to run tre-agrep 5 times, but redirect stdin and stderr to  null device
$ perf stat -r 5 bash -c 'tre-agrep "agrep" * >/dev/null 2>&1'

# SQL_ASCII encoding - get around postgres encoding problems
createdb -T template0 -E SQL_ASCII foo

# sed - edit specific line
# replace whitespace and then the number '1'  with 'num' for just the first line
$ sed '1 s/^\s\+1/num/g' foo.txt

# sed - insert header at any time
echo -e "yo\nsup" | sed '1 i\header'


# find the software that installed a linux binary
# for example, find package that installed shp_doctor
$ dpkg -S $(which shp_doctor)

# rdiff - faster than diff, but not quite human readable
# two step - make signature file, then make delta
$ rdiff signature ORIGINAL.txt SIGNATURE.sig
$ rdiff delta SIGNATURE.sig MODIFIED.txt DELTA.rdiff
# as one step in a pipe
$ rdiff signature ORIGINAL.txt | rdiff delta -- - MODIFIED.txt DELTA.rdiff
#from
#http://beerpla.net/2008/05/12/a-better-diff-or-what-to-do-when-gnu-diff-runs-out-of-memory-diff-memory-exhausted/


# move all dir including hidden dir
shopt -s dotglob nullglob
mv foo/* bar/

# validate xml against a schema
$ xmllint --noout --schema http://iatistandard.org/schema/downloads/iati-activities-schema.xsd filename.xml

# linux check CPU/GPU cycles clock MHz
# check CPU
$ lscpu
# check GPU
$ sudo lshw -C video

# tree command character encoding
# "tree" doesn't use ASCII by default
# this can be a problem for display
$ tree --charset=ASCII

# mallet - print n-grams
# make bi-grams and tri-grams from a folder of txts of 1947 USAID DEC pdfs
$ bin/mallet import-dir --input /tmp/1947/ --output dec.mallet --keep-sequence --remove-stopwords --print-output --gram-sizes 2,3


# 7z ultra/best compression
$ 7z a -t7z -m0=lzma -mx=9 -mfb=64 -md=32m -ms=on archive.7z dir1

# bind one directory to another
# like "ln"
# make files under /media/my_external/ftp show up under ./ftp
$ sudo mount --bind /media/my_external/ftp /ftp

# lftp example mirror, find, download
lftp -e 'find;exit' http://example.com
lftp -e 'mirror -c' http://example.com

# common shp2pgsql encoding issue
# switch encoding to LATIN1
$ shp2pgsql -W LATIN1 ne_10m_admin_0_countries.shp | psql scratch

# pv is awesome
# example use of pv
# shows progress bar for pipe
$ cat foo.csv | pv -f | example_command 1>/dev/null

# how to reinstall previously installed packages with apt-get
# let's say you purged a bunch of packages you didn't want to.
# whoops.
# here's how to get them back - I hope.
# NB: test incomplete
# use dpkg to list installed packages - 
# does not list purged packages as uninstalled!
# we're in luck!
# install all of them again
# note that xargs will only ask for sudo once while parallel asks each time
$ dpkg -l|grep -iE "^\bii\b"|awk '{print $2}'|xargs -I '{}' sudo apt-get -y install {}

# "apt-get -f install" troubles got you down?
# just . . .
aptitude install foo
# here's another apt-get -f install workaround
sudo dpkg --configure -a
sudo apt-get -f install

# mysql - change password / use pager less
-- change password 
mysql> SET PASSWORD FOR 'user-name-here'@'hostname-name-here' = PASSWORD('new-password-here');
-- use pager less
mysql> pager less

# openrefine - listen to requests form outside localhost
$ ./refine -i 0.0.0.0 &

# start tilestache
$ ./scripts/tilestache-server.py -i 128.239.103.87 -c acled.cfg &

# example starting tabula using custom port
$ java -Dfile.encoding=utf-8 -Xms256M -Xmx1024M -Djetty.port=9999 -jar tabula.jar

# remove utf16 BOM using dd
# skip the first two bytes of a file
# assume you've already removed null byte (eg tr -d '\000')
$ sudo dd if="crs_1973_2011.txt" of="crs_1973_2011.txt~" bs=1 skip=2

# repair a zip file
zip -FF Corrupted.zip --out New.zip

# force excel to recognize UTF-8 encoding
# I think this works?
in hex, add this BOM to beginning of file to get it recognized as UTF-8:
EFBBBF
echo "\xEF\xBB\xBF"

# how to use ASU's da vinci - a numpy alternative
# TRMM tifs use 9999 as nodata value - it seems
# convert tif to asc, using EPSG 4326, nodata=9999
$ for i in *.tif; do gdal_translate -of AAIGrid -a_srs EPSG:4326 -a_nodata 9999 -ot Float32 $i del/$( basename $i .tif ).asc; done
# make a single BSQ ascii, with bands separated by newline - for TRMM 
$ ls *.asc | parallel 'asc=$( cat {} | sed "1,6d" ); echo -e "\n$asc"' | sed '1d' > data.asc
# make a single BSQ ascii, with bands separated by newline - for boolean nodata mask
$ cat data.asc | sed 's:\b0\b:1:g;s:\b9999\b:|:g;s:[0-9]\+:1:g;s:|:0:g' > counter.asc
# import trmm
dv> data=ascii("out.asc")
# import nodata boolean mask
dv> mask=ascii("out_mask.asc")
# sum data along z axis (summing bands), ignoring nodata value
dv> data_sum = sum(data,axis=z,ignore=9999)
# sum, but for mask
dv> mask_sum = sum(mask,axis=z)
# divide the data sum by the mask sum
# NB: mask sum is a counter, where there is a "1" for each non null
dv> out=data_sum/mask_sum
# write output
dv> write(out,filename="/tmp/out.asc",type=ascii)
# back in bash, add arcinfo ascii grid header from original ascii
# NB: old nodata value is now irrelevant
$ cat ../3B42.19990120.06.7.asc| sed -n '1,6p' | cat - /tmp/out.asc | sponge /tmp/out.asc
# 
# ------------------------------------- playing with dv -----------------------------------------
# dv - ensure display() works
dv> putenv("DV_VIEWER","feh")
# print contents of an array
dv>dump(a)



# load example TRMM .bin into numpy
# load example bin
>>> trmm_data = (numpy.fromfile ( "3B42RT.2014010100.7.bin", dtype=numpy.float32, count=480*1440).byteswap()).reshape((480,1440))
# save a TSV of the array with precision 10 float
>>> np.savetxt("/tmp/foo.csv",fmt='%f.10', trmm_data, delimiter="\t")

# pretty print xml - tidy
# example with indentation
$ cat DFID-Brazil-BR.xml | tidy -xml -i

# psql - make points from tsv / csv
# make points from tsv
# steps in postgis:
# 1. create table 2. copy table from stdin 3. addGeometryColumn 4. update table set the_geom
# 4.1 (makepoint, setSRID)
$ db=scratch; tsv=/tmp/del.csv; table=$(basename $tsv .csv | sed 's:^:":g;s:$:":g'); psql -d $db -c "drop table if exists $table; create table $table ( project_id numeric, geoname_id numeric, latitude real, longitude real ); copy $table from stdin with delimiter E'\t' csv header; select addGeometryColumn ('public','del','the_geom',4326,'POINT',2); update del set the_geom = st_setSRID(st_makepoint(longitude,latitude),4326);" < $tsv


# osmconvert - clip by bbox
# example using planet pbf
$ ./osmconvert planet-131113.osm.pbf -b=29.571499,-1.478870,35.000275,4.234466 --out-osm -o=uganda_bbox-131113.osm

# generate random password with gpg 
#but beware!
#reduces system entropy - problem for cryptography especially on servers b/c of lack of mouse and keyboard as entropy source
$ gpg --gen-random --armor 1 30

# exploring a postgresql database - schemaspy
$ java -jar schemaSpy_5.0.0.jar -t pgsql -host localhost -db scratch -o /tmp -dp /usr/share/java/postgresql.jar -u adecatur -s public

# charts of XML structure using graphviz and xmlstarlet
from 
http://williamjturkel.net/2013/09/16/simple-xml-parsing-and-graph-visualization-with-command-line-tools-in-linux/
with example inputs and outputs attached.
NB: hyphens in XML are replaced with underscores b/c graphviz needs these - might be able to escape
# make dot file needed by graphviz
$ ./build_xml_tree.sh project_1.xml > xmltree.dot
# make chart
$ dot -Tpng -Goverlap=false -Grankdir=LR xmltree.dot > xmltree.png


# use wget as a spider
# get links on aiddata.org
$ url="aiddata.org"; wget --spider --force-html -r -l2 $url 2>&1 | grep '^--' > aiddata_links.txt

# find latest installed commands
# great if your newly installed software has cryptic executable names
echo $PATH | tr ':' '\n' | xargs -I '{}' bash -c 'ls -c {} | sed -n "1p"'

# uncompress Z files
uncompress foo.z
# OR, for a more generic uncompress solution "do-the-right-extraction"
dtrx foo.z

# print every character on a newline 
cat foo | sed 's/\(.\)/\1\n/g'

# when ntfs suddenly has permission denied
# add this to /etc/fstab
/dev/hda1 /mnt/C_ntfs ntfs umask=0000,rw,auto,users,exec,async,dev 0 0

# remove a given command from bash history
# remove the 865th command from bash history
$ history -d 865

# send stdout over ssh
cat foo | ssh user@remotehost "cat | wc -l"

# how to compile with automake
# can fail if not all of these are present
$ touch NEWS README AUTHORS ChangeLog
$ autoreconf --force --install
$ ./configure
$ make

# print environment variables
printenv

# turn off screen, X11
xset dpms force off

# iptables to only allow incoming on port 22
iptables -F
iptables -P INPUT DROP
iptables -A INPUT -i lo -p all -j ACCEPT
iptables -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
iptables -A INPUT -p tcp -m tcp --dport 22 -j ACCEPT
iptables -A INPUT -j DROP


# TODO 
add GNU parallel --pipe and --block
add GNU parallel --trc
