# Отправка письма по электронной почте
```toml
[dependencies]
tokio = { version = "1", features = ["full"] }
lettre = { version = "0.10.0-beta.2", default-features = false, features = ["smtp-transport", "tokio1-rustls-tls", "hostname", "r2d2", "builder"] }
```

```rust
use lettre::{
    transport::smtp::authentication::Credentials, AsyncSmtpTransport, AsyncTransport, Message,
    Tokio1Executor,
};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let smtp_credentials =
        Credentials::new("smtp_username".to_string(), "smtp_password".to_string());

    let mailer = AsyncSmtpTransport::<Tokio1Executor>::relay("smtp.email.com")?
        .credentials(smtp_credentials)
        .build();

    let from = "Hello World <hello@world.com>";
    let to = "42 <42@42.com>";
    let subject = "Hello World";
    let body = "<h1>Hello World</h1>".to_string();

    send_email_smtp(&mailer, from, to, subject, body).await
}

async fn send_email_smtp(
    mailer: &AsyncSmtpTransport<Tokio1Executor>,
    from: &str,
    to: &str,
    subject: &str,
    body: String,
) -> Result<(), Box<dyn std::error::Error>> {
    let email = Message::builder()
        .from(from.parse()?)
        .to(to.parse()?)
        .subject(subject)
        .body(body.to_string())?;

    mailer.send(email).await?;

    Ok(())
}
```
