# LaravelからGCPのCloud SQLに接続する方法

### 1\. プロジェクト作成
```
$ composer create-project laravel/laravel sample_laravel
$ cd sample_laravel
```

### 2\. .envファイルの設定
- Cloud SQLでデータベースを作成していない場合は下記参照
https://qiita.com/masakiwakabayashi/items/dd93d9a69395090f16d2
```env:設定前
DB_CONNECTION=sqlite    #Cloud SQLで設定したデータベースの種類
# DB_HOST=127.0.0.1     #Cloud SQLのパブリックIPアドレス
# DB_PORT=3306　        #mysqldのデフォルトのTCP ポートは3306
# DB_DATABASE=laravel   #Cloud SQLで設定したデータベース名
# DB_USERNAME=root      #Cloud SQLで設定したユーザー名
# DB_PASSWORD=          #Cloud SQLで設定したパスワード
```
↓↓↓ 設定後 ※例です。
```env:設定後
DB_CONNECTION=mysql
DB_HOST=12.345.67.890
DB_PORT=3306
DB_DATABASE=sample_data
DB_USERNAME=sample_root
DB_PASSWORD=password
```

### 3\. Seeder(シーダー)を使用して、データベースのテーブルに初期データを登録

#### 3-1\.Seederの作成
```
$ php artisan make:seeder 〇〇　←Seederファイル名 例)UsersTableSeeder
```
#### 3-2\. 作成したSeederファイルの設定
```php:sample_laravel/database/seeders/UsersTableSeeder.php
<?php

namespace Database\Seeders;

use Illuminate\Database\Console\Seeds\WithoutModelEvents;
use Illuminate\Database\Seeder;
use App\Models\User;

class UsersTableSeeder extends Seeder
{
    /**
     * Run the database seeds.
     */
    public function run(): void
    {
        $names = [
            'taro' => '太郎',
            'jiro' => '次郎',
            'saburo' => '三郎'
        ];

        foreach ($names as $name_en => $name_jp) {
            User::create([
                'name' => $name_jp,
                'email' => $name_en . '@example.com',
                'password' => bcrypt('xxxxxx')
            ]);
        }
    }
}
```

#### 3-3\. DatabaseSeederファイルの設定
```php:sample_laravel/database/seeders/DatabaseSeeder.php
public function run(): void
{
    $this->call(UsersTableSeeder::class);
}
```

### 4\. Seederの実行
```
$ php artisan db:seed  
```
- ※変化がない場合は下記コマンド実行
```
$ php artisan migrate:fresh --seed   
```

### 5\. データベース確認
- usersテーブルに設定した値があることを確認(太郎、次郎、三郎がいるはず)。