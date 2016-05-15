Projects
--------

Projects are located at the `_projects` directory.
They normally use layout called `project` which allows to use image galleries for a project.

To add an image gallery to a project:

1. Add images to the `/assets/projects/` directory.
2. Copy all the images to the `thumbs` subdirectory.
3. Make resize the images in the `thumbs` subdirectory by this command on a Mac:
    $ sips -Z 300 *.png
4. Add a data file to the `/_data/projects` directory, name it after the file int the `_projects` directory.
5. Put an array of the file descriptions for each of the file in the gallery: title and filename
(without subdirectories, just `MyPhoto.png`).

