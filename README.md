# <b>GLPI - Backup usando o CRONTAB do Linux<b/> 


# <br /> Define o diretório de backup do GLPI<br />
DIR="/var/www/html/glpi/backups";
#
# Define o formato do nome do arquivo de backup
DB="glpi-DB-`date +%d_%m_%Y-%H_%M`"
#
# Gerando o arquivo SQL com o mysqldump.
mysqldump --host=LOCALHOST --user=root --password=password --databases glpi >$
#
# Verifica se o diretório existe, se não ele irá criar e dar permissão
if [ ! -d $DIR ]; then
    mkdir $DIR
    chmod -R 0777 $DIR
fi
#
# Abrindo o diretório
cd $DIR
#
# Compactando o arquivo para que não fique muito grande
tar -zvcf $DB.tar.gz $DB.sql
#
# Removendo o arquivo original para liberar espaço
rm -f $DIR/$DB.sql
#
# Removendo arquivos com mais de 1 dias
find $DIR/*.tar.gz -ctime +0 -exec rm -rf {} \;
#
# Removendo o backup da instalação completa anterior
rm -f $DIR/glpi.tar.gz
#
# Refaz o backup da instalação completa
tar -zvcf $DIR/glpi.tar.gz /var/www/html/glpi
#
# Mostrar mensagem de sucesso quando for concluído
echo "Arquivo movido com sucesso!";
#
# Usar o CRONTAB e dessa forma irá executar de 6 em 6 horas
 crontab -e
<br />0 0,6,12,18 * * * sh /var/www/html/glpi/backup.sh<br />
