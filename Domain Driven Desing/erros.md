# Modelodo del Dominio: Exepciones vs Errores

## 3 razones para modelar las excepciones

- Fragilidad: Si se cambia el mensaje de error, se rompen las pruebas

```php
class Email
{
    public function __construct(string $email)
    {
        if(empty($email)) {
            throw new \InvalidArgumentException("Email cannot be empty");
        }

        if (!filter_var($email, FILTER_VALIDATE_EMAIL)) {
            throw new \InvalidArgumentException("Invalid email");
        }
    }
}

try {
    $email = new Email("");
} catch (\InvalidArgumentException $e) {

  if ($e->getMessage() !== "Email cannot be empty") {
    echo $e->getMessage();
  }

  if ($e->getMessage() !== "Invalid email") {
    echo $e->getMessage();
  }
}
```

- Especificidad: No se sabe que tipo de error se esta lanzando

```php
class EmailException extends \InvalidArgumentException {}

class Email
{
    public function __construct(string $email)
    {
        if(empty($email)) {
            throw new EmailException("Email cannot be empty");
        }
        if (!filter_var($email, FILTER_VALIDATE_EMAIL)) {
            throw new EmailException("Invalid email");
        }
    }
}

try {
    $email = new Email("");
} catch (EmailException $e) {
    echo $e->getMessage();
}
```

- Encapsulacion: Se tiene un control de los errores

```php
class EmailEmptyException extends \InvalidArgumentException {

    public function __construct()
    {
      parent::__construct("Email cannot be empty");
    }
}

class EmailInvalidException extends \InvalidArgumentException {
    public function __construct()
    {
      parent::__construct("Invalid email");
    }
}

class Email
{
    public function __construct(string $email)
    {
        if(empty($email)) {
            throw new EmailEmptyException();
        }
        if (!filter_var($email, FILTER_VALIDATE_EMAIL)) {
            throw new EmailInvalidException();
        }
    }
}

try {
    $email = new Email("");
} catch (EmailEmptyException $e) {
    echo $e->getMessage();
} catch (EmailInvalidException $e) {
    echo $e->getMessage();
}
```
