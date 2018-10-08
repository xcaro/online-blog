---
title: "Xem các câu truy vấn trong Laravel"
category: blog
layout: post
tag:
  - Laravel
  - tips
---

Khi mấy bạn viết một ứng dụng với Laravel, có thể sẽ gặp một vài vấn đề. Ví dụ như mấy bạn viết một truy vấn phức tạp, mà muốn xem thằng Laravel đã thực hiện truy vấn như thế nào đến Cơ Sở Dữ Liệu. Để làm được điều đó thì mấy bạn cần phải sử dụng [Laravel Debugbar](https://laravel-news.com/laravel-debugbar/) hoặc là [Laravel database profiler](https://github.com/dmitry-ivanov/laravel-db-profiler) ở ứng dụng của mình. Tuy nhiên, mình sẽ hướng dẫn các bạn một cách đơn giản hơn mà không cần cài thêm thư viện gì cả.

Đầu tiên bạn thêm đoạn code sau vào method `boot` của class `AppServiceProvider` trong ứng dụng.
```php
<?php

namespace App\Providers;

use DB;
use Event;
use Illuminate\Support\ServiceProvider;

class AppServiceProvider extends ServiceProvider
{
    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
      // Add this block to dump queries
      if (env('APP_ENV') === 'local') {
        DB::connection()->enableQueryLog();
      }
      if (env('APP_ENV') === 'local') {
        Event::listen('kernel.handled', function ($request, $response) {
          if ( $request->has('sql-debug') ) {
            $queries = DB::getQueryLog();
            dd($queries);
          }
        });
      }
    }

    /**
     * Register any application services.
     *
     * @return void
     */
    public function register()
    {
        //
    }
}
```

Bây giờ, trong lúc mấy bạn code thì chỉ cần thêm `?sql-debug=1` trên url là có thể xem toàn bộ các câu truy vấn được thực hiện tại màn hình hiện tại.

Chúc mấy bạn code ngon cơm.