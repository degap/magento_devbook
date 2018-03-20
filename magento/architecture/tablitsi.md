## [Добавление новой таблицы в базу данных](http://devdocs.magento.com/videos/fundamentals/add-a-new-table-to-database/)

Magento 2 имеет специальный механизм, позволяющий создавать новые таблицы, изменять существующие и даже добавлять некоторые данные в них. Эти изменения могут быть переносимыми между различными установками.

Ключевой момент заключается в использовании скриптов для установки или изменения данных вместо использования операторов SQL снова и снова при переустановке системы.

В magento 2 несколько типов таких скриптов: InstallSchema, InstallData, UpgradeSchema and UpgradeData. Установочные \(install\) скрипты выполняются только один раз, тогда как скрипты upgrade выполняются каждый раз при изменении версии модуля.

Скрипты хранятся в каталоге Setup модуля.

Скрипт Setup/InstallSchema.php реализует интерфейс Magento\Framework\Setup\InstallSchemaInterface.

```php
<?php

namespace Vendor\Module\Setup;


use Magento\Framework\DB\Ddl\Table;
use Magento\Framework\Setup\InstallSchemaInterface;
use Magento\Framework\Setup\ModuleContextInterface;
use Magento\Framework\Setup\SchemaSetupInterface;

class InstallSchema implements InstallSchemaInterface
{

    /**
     * Installs DB schema for a module
     *
     * @param SchemaSetupInterface $setup
     * @param ModuleContextInterface $context
     * @return void
     * @throws \Zend_Db_Exception
     */
    public function install(SchemaSetupInterface $setup, ModuleContextInterface $context)
    {
        $connection = $setup->getConnection();
        $tableName = $setup->getTable('table');
        $table = $connection
            ->newTable($tableName)
            ->addColumn(
                'id',
                Table::TYPE_BIGINT,
                20,
                ['auto_increment' => true, 'primary' => true, 'unsigned' => true]
            )
            ->addColumn(
                'external_id',
                Table::TYPE_BIGINT,
                20,
                ['nullable' => false, 'unsigned' => true]
            )
            ->addColumn(
                'date_from',
                Table::TYPE_DATE,
                null,
                ['nullable' => false]
            )
            ->addColumn(
                'date_to',
                Table::TYPE_DATE,
                null,
                ['nullable' => false]
            )
            ->addColumn(
                'type',
                Table::TYPE_TEXT,
                31,
                ['nullable' => false]
            )
            ->addColumn(
                'title',
                Table::TYPE_TEXT,
                255,
                ['nullable' => true]
            )
            ->addIndex(
                'idx_table_external_id',
                'type'
            )
            ->addIndex(
                'idx_table_date_from_date_to',
                ['date_from', 'date_to']
            );
        $connection->createTable($table);

        $priceTableName = $setup->getTable('table_price');
        $priceTable = $connection
            ->newTable($priceTableName)
            ->addColumn(
                'id',
                Table::TYPE_BIGINT,
                20,
                ['auto_increment' => true, 'primary' => true, 'unsigned' => true])
            ->addColumn(
                'table_id',
                Table::TYPE_BIGINT,
                20,
                ['nullable' => false, 'unsigned' => true])
            ->addColumn(
                'code',
                Table::TYPE_TEXT,
                255,
                ['nullable' => false])
            ->addColumn(
                'price',
                Table::TYPE_DECIMAL,
                10,
                ['nullable' => false])
            ->addForeignKey(
                'fk_table_price_table_id',
                'table_id',
                $tableName,
                'id')
            ->addIndex(
                'idx_table_price_table_id',
                'table_id'
            );
        $connection->createTable($priceTable);

        $kitTableName = $setup->getTable('table_kit');
        $kitTable = $connection
            ->newTable($kitTableName)
            ->addColumn(
                'id',
                Table::TYPE_BIGINT,
                20,
                ['auto_increment' => true, 'primary' => true, 'unsigned' => true])
            ->addColumn(
                'table_id',
                Table::TYPE_BIGINT,
                20,
                ['nullable' => false, 'unsigned' => true])
            ->addColumn(
                'kit_code',
                Table::TYPE_TEXT,
                255,
                ['nullable' => false])
            ->addForeignKey(
                'fk_table_kit_table_id',
                'table_id',
                $tableName,
                'id');
        $connection->createTable($kitTable);

    }
}
```

Здесь создаются три связанные таблицы, индексы и внешние ключи.

