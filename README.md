# pytest SMTPD Fixture

**Not yet uploaded to PyPi. Version 0.1.0 coming soon.**

As simple SMTP server for use as a fixture with pytest. Leans heavily on the work of akheron and published as Gist [here](https://gist.github.com/akheron/cf3863cdc424f08929e4cb7dc365ef23). As with that project, all this does is receives messages and appends them to a list.

> **Known Issue:** the order in which fixtures are listed is critical can lead to failures. See known issues below.

## Installing

Installing handled through PyPi:

```sh
pip install pytest-smtpd-fixture
```

Or, can equally be included in a `setup.py` file:

```python
setup(
    ...
    tests_require = [
        "pytest",
        "pytest-smtpd-fixture",
    ],
)
```

## Using

To use the `smtpd` fixture import it into your `conftest.py` file and then use it like any other pytest fixture. for example:

```python
# conftest.py
from smtplib import SMTP
from pytest_smtpd_fixture import smtpd

@pytest.fixture
def smtp_client(request):
    smtp = SMTP(host='127.0.0.1', port=8025)
    request.addfinalizer(smtp.close)
    return smtp
```

And then in the test:

```python
# test_mail.py
def test_sendmail(smtpd, client):
    from_addr = "from.addr@example.org"
    to_addrs = "to.addr@example.org"
    msg = f"From: {from_addr}\r\nTo: {to_addrs}\r\nSubject: Foo\r\n\r\nFoo bar"
    client.sendmail(from_addr, to_addrs, msg)
    assert len(smtpd.messages) == 1
```

### Configuration

Configuration is handled through two environment variables:

Variable | Default
---------|--------
`SMTPD_HOST` | `"127.0.0.1"`
`SMTPD_PORT` | `8025`

## Known Issues

+ The order in which fixtures are added to tests makes a difference.
+ Firewalls may interfere with the operation of the smtp server.
+ No support for SSL, TLS, STARTTLS, or authentication.

-----

Written 2020 with ☕ and ❤ in Montreal, QC
