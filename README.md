# spotify-api-token-manager
Manage your access tokens easily

## About this project
This project helps you to manage your spotify access tokens. this automates the process of refreshing tokens and updating them :3!
i love you tokio <3 

## How to use this library

setup in the [spotify dashboard](https://developer.spotify.com/dashboard) your application, there are several tutorials online how to do that

what matters for us specifically are a few things

- client id
- client secret
- redirect url

please note that the redirect url should always end with `/callback`

here is a quick example usage

```rs
use spotify_token_manager::{TokenManager, verify_creds};
use std::net::TcpListener;
use tokio::signal;

#[tokio::main]
async fn main() {
    let listener = TcpListener::bind("127.0.0.1:8080").unwrap();

    let client_id = "05fd6ff8a5d84f399b5491410b9b22e5";
    let client_secret = "32b3b73d7ac4425bbf60484a5deab9f5";

    //SOMETHING VERY NEW!!!!! 
    //verify your creds before actually starting the token manager :3

    // this returns a bool!, if its true, your creds are valid, well done, if its false, well 3x;
    if verify_creds(&client_id, &client_secret).await.unwrap() {
        let init = TokenManager::new(
            client_id.to_string(),
            client_secret.to_string(),
            vec![
                "user-read-private".to_owned(),
                "user-read-email".to_owned(),
                "user-top-read".to_string(),
            ],
            listener
        );

        init.start_server().await;

        //will return None, if you havent authorized yet.
        let result = init.get_token().await;

        println!("result: {:?}", result);
    }


    signal::ctrl_c().await.expect("failed to listen for Ctrl+C"); //keep the the thread alive, if you dont keep the thread alive unexpected issues will occur. 
}
```
note that the url of the listener always needs to be the same url you defined earlier in the spotify developer dashboard.

after you have done it, open the listener url in your browser along with `/login` to authorize your account

example of the url could be http://127.0.0.1:8080/login

## also important
please note that this library will create a folder in your home dir called "stm_data" which stands simply for spotify token manager data :D
so make sure that you dont already have a folder which is called like that
    
