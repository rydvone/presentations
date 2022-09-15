## how it works
stackoverflow instruction https://stackoverflow.com/questions/31163633/how-to-host-a-reveal-js-presentation

Here are the steps:
  1. Create a clean repository in GitHub (without README etc.), say presentations  

    git clone git@github.com:username/presentations.git  

  2. Add reveal.js as "remote" and pull the repository  

    git remote add upstream https://github.com/hakimel/reveal.js.git  
    git pull upstream master  

  3. Create an empty branch for your presentation and clean the working directory  

    git checkout --orphan rss-presentation  
    git reset --hard  

  4. Copy presentation to current folder and commit your changes  

    cp path/to/rss-presentation.html .   
    git add .  
    git commit -m 'init: start rss-presentation'  
    ... work with slides  

  5. Switch to master and merge your presentations there  

    git checkout main      or git merge main ...name-branch... --allow-unrelated-histories  
    git merge rss-presentation  
    
  6. Push all branches to GitHub  

    git push --all origin  
    
  7. Set up GitHub Pages to branch master and enjoy your presentation at https://rydvone.github.io/presentations/rss-presentation.html  

Now, whenever you want to add another presentation, you just need to repeat steps 4-7. Besides, whenever you want to update reveal.js, you can simply do git pull upstream master.  