
# Custom Ansible Plugin: Data Transformation Filter

This project demonstrates how to write a custom Ansible plugin using Python. In this example, we create a custom **filter plugin** that transforms data (e.g., formatting strings, converting case, or applying complex data manipulation) for use in Ansible playbooks.

## Why Use Custom Ansible Plugins?

While Ansible modules execute tasks on remote hosts, custom plugins extend Ansible’s core functionality in several ways. Plugins can:
- **Enhance Output:** Customize logging, display, or error handling (e.g., with callback plugins).
- **Modify Data:** Transform variables and data on the fly using filter plugins.
- **Increase Flexibility:** Manage dynamic inventories, custom connections, or even additional lookup functionality.
- **Integrate Seamlessly:** Plug into Ansible’s execution flow and reuse your code easily.

Using plugins lets you build reusable components that integrate directly with Ansible’s playbook processing, rather than writing standalone scripts.

## Types of Ansible Plugins

Ansible supports several types of plugins. Here are the main ones:

- **Action Plugins:** Extend or override the behavior of normal tasks.
- **Callback Plugins:** Customize and control the output display during playbook runs.
- **Connection Plugins:** Define how Ansible communicates with remote devices or services.
- **Filter Plugins:** Transform data and variables within playbooks (the focus of this example).
- **Lookup Plugins:** Retrieve data from external sources for use in playbooks.
- **Inventory Plugins:** Dynamically generate inventory from various data sources.
- **Strategy Plugins:** Change task execution logic (e.g., controlling parallelism).

Each type solves a different problem. For example, filter plugins allow you to modify data inline inside your Jinja2 templates or when you assign variables. This is ideal when you need complex data manipulation logic that goes beyond what the built-in filters can do.

## Use Case Overview

In our example, the custom filter plugin will:
- **Accept input data:** For example, a string or a list.
- **Apply a transformation:** Such as converting text to title case and adding a custom prefix.
- **Return the transformed data:** So it can be used directly in playbooks or templates.

## File Structure

A typical project structure might look as follows:

```
custom_ansible_plugin/
├── README.md
├── filter_plugins/
│   └── my_filters.py
└── playbook.yaml
```

## Developing the Custom Filter Plugin

### 1. Create the Filter Plugin

Create a directory named `filter_plugins` if it doesn’t already exist. In that directory, create a file named `my_filters.py` with the following content:

```python
#!/usr/bin/env python

def custom_title(data, prefix=""):
    """
    Convert the input string to title case and add a prefix.
    Example: custom_title("hello ansible", "Custom: ") returns "Custom: Hello Ansible"
    """
    if not isinstance(data, str):
        return data
    return prefix + data.title()

class FilterModule(object):
    """ Custom filters for Ansible """
    def filters(self):
        return {
            'custom_title': custom_title,
        }
```

*Explanation:*
- We define a function `custom_title` that takes a string, converts it to title case, and prepends a custom prefix.
- The `FilterModule` class exposes our custom filter by returning a dictionary with the filter name mapping to the function.

### 2. Create an Example Playbook

Create a file named `playbook.yaml`:

```yaml
- name: Test custom filter plugin
  hosts: localhost
  connection: local
  gather_facts: false
  vars:
    sample_text: "this is a custom ansible plugin example"
  tasks:
    - name: Transform text using custom_title filter
      debug:
        msg: "{{ sample_text | custom_title('Custom: ') }}"
```

*Explanation:*
- We set a variable `sample_text` in the playbook.
- We use the custom filter `custom_title` within a debug task to transform the text.

## Running the Plugin

1. **Ensure that Ansible picks up your plugin:**  
   By default, Ansible automatically searches for custom filter plugins under the `filter_plugins` directory in your playbook’s directory.

2. **Run the playbook:**

   ```bash
   ansible-playbook playbook.yaml
   ```

3. **Verify the Output:**  
   You should see the debug message output displaying your transformed string, for example:
   
   ```
   TASK [Transform text using custom_title filter] *******************************
   ok: [localhost] => {
       "msg": "Custom: This Is A Custom Ansible Plugin Example"
   }
   ```

## Conclusion

This project demonstrates:
- How to develop a custom filter plugin to extend Ansible's data manipulation capabilities.
- The benefits of using plugins—including integration, idempotence, and reuse—instead of standalone scripts.
- An overview of the different types of Ansible plugins available and their use cases.

For more detailed information, refer to:
- [Developing Ansible Plugins](https://docs.ansible.com/ansible/latest/dev_guide/developing_plugins.html)
- [Ansible Filter Plugins Documentation](https://docs.ansible.com/ansible/latest/user_guide/playbooks_filters.html)

Feel free to expand this example to explore other plugin types and integrated workflows.

