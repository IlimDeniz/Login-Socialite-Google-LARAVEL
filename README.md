# Google Login Integration with Laravel Socialite

## Information

Hello everyone! Today, we will be installing the **Socialite** library, which allows users to log in with their Google accounts. The installation process for this library is straightforward and easy.

## Step 1: Install Laravel Socialite Package

Install the **Laravel Socialite** package using composer.

```bash
composer require laravel/socialite
```

## STEP 2

When the installation is complete, Paste the “clientID”, “clientSecret” and redirect URI in .env file as:

```bash
GOOGLE_CLIENT_ID=xxxxxxxxxxxx-runcntjqat1j56ghqlip78eejl4pdakm.apps.googleusercontent.com
GOOGLE_CLIENT_SECRET=xxxxxx-sl9vkVFFmovXgAOp2ZSektxxxxxx
GOOGLE_REDIRECT_URL=http://127.0.0.1:8000/google/callback
```

## STEP 3

Now add google service in config/services.php.

```bash
'google' => [
   'client_id' => env('GOOGLE_CLIENT_ID'),
   'client_secret' => env('GOOGLE_CLIENT_SECRET'),
   'redirect' => env('GOOGLE_REDIRECT_URL'),
]
```

## STEP 4

Routes and Controller Methods

Add the following two routes in web.php

```bash
Route::get('/google/redirect', [App\Http\Controllers\GoogleLoginController::class, 'redirectToGoogle'])->name('google.redirect');
Route::get('/google/callback', [App\Http\Controllers\GoogleLoginController::class, 'handleGoogleCallback'])->name('google.callback');
```

Now create a controller “GoogleLoginController” and add the following content to it

```bash
<?php

namespace App\Http\Controllers;

use App\Models\User;
use Illuminate\Http\Request;
use Laravel\Socialite\Facades\Socialite;
use Illuminate\Support\Facades\Auth;
use App\Providers\RouteServiceProvider;

class GoogleLoginController extends Controller
{
    public function redirectToGoogle()
    {
        return Socialite::driver('google')->redirect();
    }


    public function handleGoogleCallback()
    {
        $googleUser = Socialite::driver('google')->stateless()->user();
        $user = User::where('email', $googleUser->email)->first();
        if(!$user)
        {
            $user = User::create(['name' => $googleUser->name, 'email' => $googleUser->email, 'password' => \Hash::make(rand(100000,999999))]);
        }

        Auth::login($user);

        return redirect(RouteServiceProvider::HOME);
    }
}
```

## STEP 5

Create Google Login Button in the FrontEnd

Now add an anchor tag in the login blade file as:

```bash
<a href="{{ route('google.redirect') }}" class="btn btn-primary"> Login with Google </a>
```
