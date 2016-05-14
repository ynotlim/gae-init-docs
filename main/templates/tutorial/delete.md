{% raw %}

In order to delete the contact we will have to do two things: create the
handler that will delete the contact from the datastore and add the delete link 
on the contact list.

### Contact Delete Handler

Add the following code to the `contact.py` file:

```python
@app.route('/contact/<int:contact_id>/delete/', methods=['GET', 'POST'])
@auth.login_required
def contact_delete(contact_id):
  contact_db = model.Contact.get_by_id(contact_id)
  if not contact_db or contact_db.user_key != auth.current_user_key():
    flask.abort(404)
  contact_db.key.delete()
  return flask.redirect(flask.url_for('contact_list', order='-created'))
```

#### Add a delete link on the contact list

Next we'll need to add a link to the `contact_list.html` file.  Add a new column and add
the following code after the address column:

```html
...
          <td>
            <a href="{{url_for('contact_delete', contact_id=contact_db.key.id())}}">
              <span class="fa fa-fw fa-trash-o"></span>
            </a>
          </td>
...
```

Now visit the contact list
([http://localhost:8080/contact/](http://localhost:8080/contact/)),
and you should be able to click on the trash icon to delete the contact.

{% endraw %}
