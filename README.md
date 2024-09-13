# Flatpak Manifest

This is a draft of the Flatpak manifest file which allows SasView to be built as a Flatpak.

To build, you need to have [Flatpak](https://www.flatpak.org/setup/), and `flatpak-builder`. If you don't have `flatpak-builder` installed, you can run the following command to install it:

``` shell
flatpak install -y flathub org.flatpak.Builder
```

Then, to build the manifest, run the following command.

``` shell
flatpak-builder --force-clean build/ manifest.yml
```

To install it to your local machine, add the `--install` flag to the above command. Then, you can run the Flatpak with:

``` shell
flatpak run org.sasview.sasview
```

This requires that there be a `sasview` directory which contains a working PyInstaller distribution of SasView.

# Notes

- Right now, the manifest file relies on the PyInstaller distribution to be on the user's local machine. This should not be the case as this distribution should be fetched from the network. However, at present, the distribution produced by GitHub Actions does not work in the Flatpak as the version of Ubuntu used to build the distribution is too old. Thus, a newer version needs to be used to build the Flatpak.
- Flathub's [best practices](https://docs.flathub.org/docs/for-app-authors/requirements#best-practices) state that 'Applications should build all components of the manifest from source when possible.' This is currently not the case for this manifest as the SasView module is using the PyInstaller distribution which has already been built. I have tried to get a Flatpak that packages SasView from source but I have been unable to do so. This seems to be a good temporary solution that works for now.
- SasView requires there to be a C compiler in its environment, otherwise it will crash when trying to select a model. Since a C compiler is only available in the SDK, and not the runtime, I have included `tinycc` as a module because I understand it is the same compiler that is included in the Windows distribution, and it is fairly easy to build from source. But a different compiler could be selected.
- I have given the Flatpak full access to the home directory but this may not be appropriate. It is possible to be more specific with which directories need to be accessible, and also any file selected by the user using the file chooser dialog can still be read as this uses Flatpak's [portal system](https://docs.flatpak.org/en/latest/portal-api-reference.html).
- One of the modules is fetching a library from the Fedora repositories. This was only meant to be a temporary solution but I will be looking into a more permanent solution.

# Known Issues
- The documentation viewer does not work currently. I think this is because the documentation files are simply not being generated. 
- There are no OpenCL drivers in the Flatpak so any functionality related to OpenCL will not work.
- There is no desktop file so the application can't be launched from the desktop.
- In the about page, SasView incorrectly states its installation path as `/app/bin` because, since its in a containerised environment, it only knows where it is installed in that environment itself, and not on the host system.

