# PyMailAccess

Módulo Python para **autenticar, pesquisar, ler e enviar e-mails via IMAP/SMTP**. O pacote usa o nome `pymailaccess`; este repositório se chama **PyMailManager**.

O projeto organiza a conexão, a leitura e o envio em classes separadas, com tratamento de erros e testes unitários que simulam os servidores de e-mail.

## Funcionalidades implementadas

- Autenticação e encerramento de conexões IMAP e SMTP.
- Listagem e seleção de pastas IMAP.
- Busca e leitura de mensagens por identificador.
- Extração do corpo de mensagens em texto e HTML.
- Envio de mensagens em texto simples ou HTML para um destinatário.
- Exceções para falhas de conexão e autenticação.

## Instalação

Requer Python **3.10 ou superior**, conforme os metadados do pacote.

```bash
git clone https://github.com/JonasChristiano/PyMailManager.git
cd PyMailManager
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
```

No Windows, ative o ambiente com `.venv\Scripts\Activate.ps1` no PowerShell. Execute os exemplos a partir da raiz do repositório para importar o módulo `mail`.

## Ler mensagens

Defina `MAIL_ADDRESS`, `MAIL_PASSWORD` e `IMAP_SERVER` no ambiente. Utilize o método de autenticação permitido pelo seu provedor; quando aplicável, use senha de aplicativo.

```python
import os
from mail.reader import Reader

reader = Reader(
    email=os.environ["MAIL_ADDRESS"],
    password=os.environ["MAIL_PASSWORD"],
    imap_server=os.environ["IMAP_SERVER"],
)
try:
    reader.select_folder("INBOX")
    for uid in reader.search_emails("UNSEEN"):
        print(reader.get_email_body(uid))
finally:
    reader.disconnect_imap()
```

## Enviar uma mensagem

Defina também `SMTP_SERVER` com o hostname do servidor. O código atual usa `smtplib.SMTP` com STARTTLS e a porta padrão dessa classe; portas configuráveis e conexão SSL direta ainda precisam de implementação. Não passe `host:porta` como hostname.

```python
import os
from mail.sender import Sender

sender = Sender(
    email=os.environ["MAIL_ADDRESS"],
    password=os.environ["MAIL_PASSWORD"],
    smtp_server=os.environ["SMTP_SERVER"],
)
try:
    sender.send_email(
        subject="Exemplo de integração",
        to="destinatario@example.com",
        body="Mensagem enviada pela biblioteca.",
        content_type="plain",
    )
finally:
    sender.disconnect_smtp()
```

Para HTML, use `content_type="html"` e um corpo com marcação HTML. O parâmetro `to` recebe uma string com o endereço do destinatário; envio para listas não está implementado nesta versão.

## Testes

```bash
python -m unittest discover -s tests -v
```

Os testes utilizam mocks e não precisam enviar e-mails nem acessar uma conta real.

## Estrutura

- `mail/connection.py`: conexão e exceções IMAP/SMTP.
- `mail/reader.py`: pastas, busca e leitura de mensagens.
- `mail/sender.py`: envio de texto simples e HTML.
- `tests/`: testes de conexão, leitura e envio.

## Próximas melhorias

Portas SMTP configuráveis, OAuth, anexos, destinatários múltiplos e tratamento mais amplo de charsets são melhorias futuras. Elas não são apresentadas como funcionalidades disponíveis.

## Licença

[MIT](LICENSE). Desenvolvido por [Jonas Christiano](https://github.com/JonasChristiano).
