# Yama CMS - Hugo starter example

This is a starter/example Hugo repository, configured to interface with Yama CMS.

Yama CMS will write .md files to ./content.
Once you've wired up your repository with Yama CMS, delete `./content/_index.md` and `./content/posts/` files and update the configuration to match your needs. 

---

## Installation and usage

You can either run this repository directly if you have Hugo installed; or you can use [Earthly](https://docs.yama-cms.com/docs/guide/build-deploy-earthly) (think Dockerfiles + Makefiles) to run the needed tools inside containers.

Running directly (to install, see [Hugo installation](https://gohugo.io/installation/)):
```bash
hugo server
```
Running via Earthly (to install, see [Earthly's Get Started](https://earthly.dev/get-earthly)):
```bash
earthly +dev
```
For more information on how to use Earthly, see [our YamaCMS specific documentation](https://docs.yama-cms.com/docs/guide/build-deploy-earthly/) or [the official Earthly documentation](https://docs.earthly.dev/basics).


***

## Editing this README

When you're ready to make this README your own, just edit this file. If you need inspiration, see [makeareadme.com](https://www.makeareadme.com/) for templates and ideas.
