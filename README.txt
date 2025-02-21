Build procedure

To build: In the "model-viewer/packages" subfolder of model-viewer run: npm run build
To run a test server: In the "model-viewer/packages" subfolder of model-viewer run: npm run serve
NOTE: You will get a Circular dependencies error. This can be ignored.

A test page using this test server is: "https://192.168.0.20:3001/model-viewer-test.html". You will probable have to go to "https://192.168.0.20:8080" first to
grant certificate privilege.
The try-on button will use vyking-apparel running at https://192.168.0.20:1234. This server needs to be run using: "npm run server". Note onlu loginfo statements will
be visible in the debugger.


Release procedure


In the "model-viewer/packages" subfolder of model-viewer
Update VykingMixinVersion in vyking.ts in package.json
Update docs
Run: npm run docs
Run: npm run build

git add and commit whats checked out.
get checkout -b <release-VERSION>
Run: npm run docs
Run: npm run build
Copy "dist/docs" folder into "packages/model-viewer/dist" folder replacing any existing docs folder.
Copy "packages/model-viewer/dist" folder to release folder: ../release/<google model-viewer version number> (eg 3.3.0)
Rename "dist" to the release version number (eg 1.8)

Deploy to AWS

Login to AWS
Go to s3 folder: vyking-sneaker-window/vyking-model-viewer/3.3.0/
Create a folder of the correct version (eg 1.8)
Upload release just made, but only copying the docs and model-viewer.min.js file into the folder just created.
Test release by replacing 
    <script type="module" src="https://sneaker-window.vyking.io/vyking-model-viewer/model-viewer.min.js"></script>
in vykingSneakerWindow/test/model-viewer-catalog-example.html with 
    <script type="module" src="https://sneaker-window.vyking.io/vyking-model-viewer/3.3.0/VERSION/model-viewer.min.js"></script>
Go to s3 folder: vyking-sneaker-window/vyking-model-viewer
Create a folder in the "backup" folder with today's date.
Copy model-viewer.min.js into this folder.
Upload latest release's model-viewer.min.j file to replace the current one.
Upgrade docs by uploading new docs folder over the top of the existing one.
Upgrade appropriate examples from VykingSneakerWindow.

To Update Cloudfront with latest release
Go to Cloudfront
Select distribution E33UIF1FTFSRE2
Go to the Invalidatins tab
Create a new invalidation containing: /vyking-model-viewer/model-viewer.min.js


Commit new release to git

When on release branch:
git tag -a "v<release number>" -m "<release note>" defines a tag for that release.
git checkout master
git merge <release branch>
git push
git push origin <tag name> pushes the tag onto the remote origin.
git checkout dev
git merge master (Should say Already up to date.)


Notes

Need to sync with Google's upstream model-viewer regularly. Depending on how much has changed you may want to
do this in a separate clone. You cannot --depth=1 with this clone, because it will not allow the merge.

git remote -v
This should show an upstream remote to Google's model-viewer:
    upstream	git@github.com:google/model-viewer.git (fetch)
    upstream	git@github.com:google/model-viewer.git (push)   
If not run: git remote add upstream  git@github.com:google/model-viewer.git
git fetch upstream

When on dev branch: git checkout -b dev-upstream-merge
 git merge upstream/master
 Fix the conflicts. If there are a lot of conflicts in package-lock.json then just resolve them all to the new changes and
 then run: npm run update:package-lock.
