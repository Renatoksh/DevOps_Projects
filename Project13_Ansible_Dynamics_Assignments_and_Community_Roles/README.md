#
# PROJECT 13: ANSIBLE DYNAMIC ASSIGNMENTS (INCLUDE) AND COMMUNITY ROLES
### IMPORTANT NOTICE: Ansible is an actively developing software project, so you are encouraged to visit [Ansible Documentation](https://docs.ansible.com/) for the latest updates on modules and their usage.
### Last 2 projects have already equipped you with some knowledge and skills on Ansible, so you can perform configurations using playbooks, roles and imports. Now you will continue configuring your UAT servers learning and practicing new Ansible concepts and modules.
### In this project we will introduce [dynamic assignments](https://docs.ansible.com/ansible/latest/user_guide/playbooks_reuse.html#includes-dynamic-re-use) by using include module.
### Now you may be wondering, what is the difference between static and dynamic assignments?
### Well, from Project 12, you can already tell that static assignments use import Ansible module. The module that enables dynamic assignments is include.
### Hence,
### import = Static
### include = Dynamic
### When the import module is used, all statements are pre-processed at the time playbooks are parsed. Meaning, when you execute site.yml playbook, Ansible will process all the playbooks referenced during the time it is parsing the statements. This also means that, during actual execution, if any statement changes, such statements will not be considered. Hence, it is static.
### On the other hand, when include module is used, all statements are processed only during execution of the playbook. Meaning, after the statements are parsed, any changes to the statements encountered during execution will be used.
### Take note that in most cases it is recommended to use static assignments for playbooks, because it is more reliable. With dynamic ones, it is hard to debug playbook problems due to its dynamic nature. However, you can use dynamic assignments for environment specific variables as we will be introducing in this project.
#
## INTRODUCING DYNAMIC ASSIGNMENT INTO OUR STRUCTURE
### Introducing Dynamic Assignment Into Our structure
### In your [https://github.com/renatoksh/ansible-config-artifact](https://github.com/renatoksh/ansible-config-artifact) GitHub repository start a new branch and call it dynamic-assignments.
![](./img/branch_dynamic_assignments.PNG)
### Create a new folder, name it dynamic-assignments. Then inside this folder, create a new file and name it env-vars.yml. We will instruct site.yml to include this playbook later. For now, let us keep building up the structure.
![](./img/env_vars.PNG)
### Your GitHub shall have following structure by now.
### Note: Depending on what method you used in the previous project you may have or not have roles folder in your GitHub repository – if you used ansible-galaxy, then roles directory was only created on your Jenkins-Ansible server locally. It is recommended to have all the codes managed and tracked in GitHub, so you might want to recreate this structure manually in this case – it is up to you.
![](./img/structure.PNG)
### Since we will be using the same Ansible to configure multiple environments, and each of these environments will have certain unique attributes, such as servername, ip-address etc., we will need a way to set values to variables per specific environment.
### For this reason, we will now create a folder to keep each environment’s variables file. Therefore, create a new folder env-vars, then for each environment, create new YAML files which we will use to set variables.
### Your layout should now look like this.
![](./img/github_structure.PNG)
### Notice 3 things to notice here:
### 1. We used include_vars syntax instead of include, this is because Ansible developers decided to separate different features of the module. From Ansible version 2.8, the include module is deprecated and variants of include_* must be used. These are:
### include_role
### include_tasks
### include_vars
### In the same version, variants of import were also introduces, such as:
### import_role
### import_tasks
### 2. We made use of a [special variables](https://docs.ansible.com/ansible/latest/reference_appendices/special_variables.html) { playbook_dir } and { inventory_file }. { playbook_dir } will help Ansible to determine the location of the running playbook, and from there navigate to other path on the filesystem. { inventory_file } on the other hand will dynamically resolve to the name of the inventory file being used, then append .yml so that it picks up the required file within the env-vars folder.
### 3. We are including the variables using a loop. with_first_found implies that, looping through the list of files, the first one found is used. This is good so that we can always set default values in case an environment specific env file does not exist.
#
## UPDATE SITE.YML WITH DYNAMIC ASSIGNMENTS
### Update site.yml with dynamic assignments
### Update site.yml file to make use of the dynamic assignment. (At this point, we cannot test it yet. We are just setting the stage for what is yet to come. So hang on to your hats)
### site.yml should now look like this.
![](./img/site_yml.PNG)
## Download Mysql Ansible Role
### You can browse available community roles here
### We will be using a MySQL role developed by geerlingguy.
### Hint: To preserve your your GitHub in actual state after you install a new role – make a commit and push to master your ‘ansible-config-mgt’ directory. Of course you must have git installed and configured on Jenkins-Ansible server and, for more convenient work with codes, you can configure Visual Studio Code to work with this directory. In this case, you will no longer need webhook and Jenkins jobs to update your codes on Jenkins-Ansible server, so you can disable it – we will be using Jenkins later for a better purpose.
### On Jenkins-Ansible server make sure that git is installed with git --version, then go to ‘ansible-config-mgt’ directory and run
### git init
### git pull https://github.com/<your-name>/ansible-config-mgt.git
### git remote add origin https://github.com/<your-name>/ansible-config-mgt.git
### git branch roles-feature
### git switch roles-feature
### Inside roles directory create your new MySQL role with ansible-galaxy install geerlingguy.mysql and rename the folder to mysql
### mv geerlingguy.mysql/ mysql
![](./img/mysql_role.PNG)
### Read README.md file, and edit roles configuration to use correct credentials for MySQL required for the tooling website
![](./img/readme.PNG)
#
## LOAD BALANCER ROLES
### Load Balancer roles
### We want to be able to choose which Load Balancer to use, Nginx or Apache, so we need to have two roles respectively:
### 1. Nginx
### 2. Apache
### With your experience on Ansible so far you can:
### Decide if you want to develop your own roles, or find available ones from the community
### Update both static-assignment and site.yml files to refer the roles
### Important Hints:
### Since you cannot use both Nginx and Apache load balancer, you need to add a condition to enable either one – this is where you can make use of variables.
### Declare a variable in defaults/main.yml file inside the Nginx and Apache roles. Name each variables enable_nginx_lb and enable_apache_lb respectively.
### Set both values to false like this enable_nginx_lb: false and enable_apache_lb: false.
### Declare another variable in both roles load_balancer_is_required and set its value to false as well
### Update both assignment and site.yml files respectively
### loadbalancers.yml file
![](./img/loadbalancers_yml.PNG)
### site.yml file
![](./img/site_yml_.PNG)
### Now you can make use of env-vars\uat.yml file to define which loadbalancer to use in UAT environment by setting respective environmental variable to true.
### You will activate load balancer, and enable nginx by setting these in the respective environment’s env-vars file.
![](./img/env-vars_file.PNG)
### The same must work with apache LB, so you can switch it by setting respective environmental variable to true and other to false.
### ngnix (true & false) & apache (true & false)
![](./img/nginx_results.png)
### To test this, you can update inventory for each environment and run Ansible against each environment.
### Congratulations!
### We have learned and practiced how to use Ansible configuration management tool to prepare UAT environment for Tooling web solution.
![](./img/Completed.png)
