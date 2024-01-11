# dpp_docs
A new template for the Design for Physical Prototyping project documentation. 
It uses the static site generator [Hugo](https://gohugo.io/) to generate a web pages from [Markdown](https://www.markdownguide.org/) files with the [Hugo Book Theme](https://github.com/alex-shpak/hugo-book) and the theme component [hugo-video](https://github.com/martignoni/hugo-video).

To get started with your documentation, generate your own repository by clicking "[use this template]" or clone this repository.
Using GitHub pages you can access your documentation via [https://YOUR-GITHUB-USERNAME.github.io/YOUR-REPO-NAME](https://pretoms.github.io/dpp_docs/).

# Quickstart

For working on your documentation it is recommended to host your local Hugo instance and to use a text editor of your choice to edit the Markdown files. 

First install Hugo.
## Windows
Use PowerShell to install the extended version of Hugo with winget. Add <kbd>%localappdata%\microsoft\winget\packages</kbd> to your [path variable](https://windowsloop.com/how-to-add-to-windows-path/).
```bash
winget install Hugo.Hugo.Extended
```

## Linux
```bash
sudo apt install hugo
```

## Mac OS
Install Hugo using the free and open source package manager [Homebrew](https://brew.sh/). This will install the extended edition of Hugo.

```bash
brew install hugo
```

# Clone

Make sure you have [Git](https://git-scm.com/) installed on your system.
Clone your repository to your system.
Change active directory to the cloned repository and run the Hugo server.

```bash
git clone <your-repo> <target-directory>
cd C:\your\local\repository\path
hugo server --minify
```

Then visit [http://localhost:1313](http://localhost:1313) to view the documentation.

## Replace the content of the template pages

Update the following files to your own content:

* rename project folder <kbd>content/docs/projects/our_project</kbd> to the title of your project and remove all files but <kbd>_index.md</kbd>.
* <kbd>README.md</kbd> (information for those who access your repository site on GitHub: replace it with your text or just delete content and leave it empty)

Edit the content in the <kbd>content/docs/projects/our_project</kbd> folder. This is the only folder to be handed in at the end of the semester. Rename the folder to your projects title. The total size should not exceed 20 MB. GIFs are encouraged to demonstrate movement in your prototype. The current content content provides an exemplary structure for the documentation but can be freely adapted to your needs. Write in a way so that the documentation would be useful for your pre-project self.

[use this template]: https://github.com/pretoms/dpp_docs/generate

## Host on GitHub Pages (optional)

To host your hugo instance on GitHub Pages you have to generate a public repository from this template.
Then go to the **Settings** tab of your repository and then to the **Pages** section.
Under **Build and deployment** in the **Source** dropdown menu, select **GitHub Actions**.
You can then check the status of your deployment by clicking **View workflow runs**.
If your Initial commit failed, under the **Actions** section you can go to **Deploy Hugo site to Pages**.
There you can click on **Run workflow** to restart the deployment. After a few minutes if the deployment succeeded your page should be available under https://YOUR-GITHUB-USERNAME.github.io/YOUR-REPO-NAME

