# Migration

#### [Home](../PHP.md) > `Migration`

## Keys:
- [Convention Naming](#convention-naming)
- [Yii Migration CLI – Practical Guide](#yii-migration-cli--practical-guide)

## Convention naming:
- `mYYMMDD_HHMMSS_description` : timestamp + descriptive name
- Example: `m240323_120000_create_post_table`

```shell
php yii migrate/create m240323_120000_create_post_table
```

## Yii Migration CLI – Practical Guide
- [Create Migration](#1-create-migration) : Generate a new migration file
- [Run Migrations](#2-run-migrations) : Apply pending migrations
- [Rollback Migrations](#3-rollback-migrations) : Revert migrations
- [Migration History](#4-migration-history) : View executed migrations
- [Migrate To Version](#5-migrate-to-version) : Move to a specific version
- [Migration Fresh](#6-migration-fresh-reset) : Reset and re-run all migrations

---

### 1. Create Migration

```
php yii migrate/create create_post_table
```

* `migrate/create` : command to generate a new migration
* `create_post_table` : descriptive name (used in filename)
* Output file: `mYYMMDD_HHMMSS_create_post_table.php`

---

### 2. Run Migrations

```
php yii migrate
```

* Applies all pending migrations
* Yii checks `migration` table to know what was already executed
* Runs in chronological (timestamp) order

---

### 3. Rollback Migrations

```
php yii migrate/down 1
```

* `down` : revert migrations
* `1` : number of steps to rollback
* Calls `safeDown()` or `down()` in migration file

---

### 4. Migration History

```
php yii migrate/history
```

* Shows executed migrations
* Helps track applied versions
* Useful for debugging and auditing

---

### 5. Migrate To Version

```
php yii migrate/to m240323_120000_create_post_table
```

* Moves DB to a specific migration version
* Can go forward or backward
* Useful for syncing environments

---

### 6. Migration Fresh (Reset)

```
php yii migrate/fresh
```

* Drops all tables and re-runs migrations
* WARNING: deletes all data
* Used mainly in development/testing

---

## 2. Workflow

Standard workflow when working with Yii migrations:

1. Create a migration
   → `php yii migrate/create <migration_name>`

2. Define schema changes
   → write `safeUp()` and `safeDown()`

3. Apply migration
   → `php yii migrate`

4. Verify database changes

5. (Optional) Rollback
   → `php yii migrate/down`

6. Commit migration file to version control

---

## 3. Complete Example

### Step 1: Create migration

```
php yii migrate/create create_post_table
```

---

### Step 2: Write migration

```php
use yii\db\Migration;

class m240323_120000_create_post_table extends Migration
{
     /**
     * {@inheritdoc}
     */
    public function safeUp()
    {
        // 1. Create table
        $this->createTable('post', [
            'id' => $this->primaryKey(),
            'title' => $this->string()->notNull(),
            'content' => $this->text(),
            'status' => $this->integer()->defaultValue(0),
            'user_id' => $this->integer(),
            'created_at' => $this->integer(),
        ]);

        //2. create user table
        $this->createTable('user', [
            'id' => $this->primaryKey(),
            'username' => $this->string()->notNull(),
            'password' => $this->text(),
            'status' => $this->integer()->defaultValue(0),
            'created_at' => $this->integer(),
        ]);

        // 4. Insert sample data
        $this->batchInsert('user',
            ['username', 'password', 'status', 'created_at'],
            [
                ['user01', '123456', 1, time()],
                ['user02', '123456', 0, time()],
            ]
        );

        // 2. Create index
        $this->createIndex(
            'idx-post-title',
            'post',
            'title'
        );

        // 3. Foreign key (giả sử có bảng user)
        $this->addForeignKey(
            'fk-post-user_id',
            'post',
            'user_id',
            'user',
            'id',
            'CASCADE'
        );

        // 4. Insert sample data
        $this->batchInsert('post',
            ['title', 'content', 'status', 'user_id', 'created_at'],
            [
                ['First Post', 'This is content 1', 1, 1, time()],
                ['Second Post', 'This is content 2', 0, 1, time()],
            ]
        );
    }

    public function safeDown()
    {
        // rollback theo thứ tự ngược lại

        // 1. drop foreign key
        $this->dropForeignKey(
            'fk-post-user_id',
            'post'
        );

        // 2. drop index
        $this->dropIndex(
            'idx-post-title',
            'post'
        );

        // 3. drop table
        $this->dropTable('post');
    }
}
```

---

### Step 3: Run migration

```
php yii migrate
```

---

### Step 4: Rollback (optional)

```
php yii migrate/down 1
```

---

### Result

* Table `post` created
* Index added
* Can be safely reverted

---

## Final Note
* Always use **one migration per change**
* Never modify old migrations after execution
* Prefer **PHP API for schema**, use SQL only when necessary



