{% raw %}

In order to delete the contact we will have to do two things: create the
handler that will delete the contact from the datastore and add the delete link 
on the contact list.

### Contact Delete Handler

Add the following code to the `contact.py` file:

```python
@app.route('/contact/<int:contact_id>/delete')
@auth.login_required
def contact_delete(contact_id):
  contact_db = model.Contact.get_by_id(contact_id)
  if not contact_db or contact_db.user_key != auth.current_user_key():
    flask.abort(404)
  delete_contact_dbs([contact_db.key])
  return flask.redirect(flask.url_for('contact_list', order='-created'))
```

As you notice above, we had to add a `delete_contact_dbs` handler.  
To do that, add the following code at the bottom of the `contact.py` file:

```python
@ndb.transactional(xg=True)
def delete_contact_dbs(contact_db_keys):
  ndb.delete_multi(contact_db_keys)
```

You will also need to import ndb from the appengine library:

```python
from google.appengine.ext import ndb
```

#### Add a delete link on the contact list

Add an additional column to your table and add the following:

```html
...
<td>
  <a href="{{url_for('contact_update', contact_id=contact_db.key.id())}}">
    {{contact_db.key.id()}}
  </a>
</td>
...
```

Now visit the contact list
([http://localhost:8080/contact/](http://localhost:8080/contact/)),
and you should be able to click on the trash icon to delete the contact.

{% endraw %}
