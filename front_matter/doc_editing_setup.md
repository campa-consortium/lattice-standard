# How to Write Documentation

The documentation uses the [MyST](https://mystmd.org/) markup language. 

In order to edit and view the documentation locally, please first install MyST following [MyST's installation instructions](https://mystmd.org/guide/installing).

Check that MyST has been installed successfully by running
```{code} bash
myst --version
```
Once MyST has been installed, run
```{code} bash
myst start
```
from the root directory of the repository (where the MyST configuration file `myst.yml` is located) to start MyST locally.
This will display something like
```{code} bash
$ myst start

📖 Built front_matter/introduction.md in 96 ms.
📖 Built front_matter/governance.md in 46 ms.
📖 Built front_matter/contributing.md in 45 ms.
📖 Built front_matter/doc_editing_setup.md in 45 ms.
📖 Built standard/introduction.md in 45 ms.
📖 Built standard/element_parameter.md in 44 ms.
📚 Built 6 pages for project in 193 ms.

	✨✨✨  Starting Book Theme  ✨✨✨

🔌 Server started on port 3000!  🥳 🎉

	👉  http://localhost:3000  👈
```

Open the URL displayed in the terminal (in this case `http://localhost:3000`) to visualize the MyST content with your web browser.

You are now ready to edit the markdown files that compose the documentation!
The content displayed in your web browser will update automatically as you edit.

If you add new markdown files, do not forget to add them to the table of contents defined in the MyST configuration file `myst.yml`.
