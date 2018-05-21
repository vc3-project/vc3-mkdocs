# vc3-mkdocs
Documentation for VC3 built with MK-Docs

OK- important information here.

I've made this automated with webhooks. Now if you commit to the vc3-
mkdocs *master* branch, Jenkins will receive a webhook from Github,
scoop up the changes, run 'mkdocs build...' and so on, and then push
the compiled pages back to Github in the gh-pages branch, which then
creates the docs website.

So, in short:
   git clone .. /     git pull origin
   git checkout master
   <your changes, git add/commit etc>
   git push origin master

Jenkins should do the rest..

--Lincoln
