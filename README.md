# Documentation for Enterprise DC/OS
These files are used to generate the content for [docs.mesosphere.com](docs.mesosphere.com). 

Issue tracking is located in [Mesosphere JIRA](https://mesosphere.atlassian.net/) ([DOCS project](https://mesosphere.atlassian.net/secure/RapidBoard.jspa?rapidView=70&useStoredSettings=true).


 
## Contributing to the Documentation

The docs team prefers to receive documentation contributions as pull requests rather than having engineering teams push directly to the docs repos. This gives us a chance to review the changes for consistency and understand the new content.

If you are planning to initiate a large documentation effort, please coordinate with the docs team in advance to make sure we're not going to step on each other. You can reach the docs team by email at docss@mesosphere.io.

If you are trying to figure out where a particular bit of information should live, please reach out and ask. We're happy to help you ensure information goes to the right place.

Note that content often lives in more than one context. Please make sure that any contribution you offer takes these multiple contexts into account. So, for example, if you are working on docs that are also used in open source, please do not refer to Mesosphere's commercial offerings.

### Format
The source files are WordPress flavored Markdown.

- Links must include the full directory path, including version, relative to the root of dcos.io (e.g. `/docs/1.7/administration/sshcluster/`). Note that these links will not work in the GitHub code browser. It is recommended that you test your content [locally](#test-local) before submitting your PR.
- Final page links are directory names, not filenames (e.g. `https://dcos.io/docs/latest/usage/service-discovery/mesos-dns/`).
- You must have an `index.md` page for all parent directories (rather than using Github's README.md indexing). For example, the parent directory `/dcos-docs/1.7/administration/` must also contain `/dcos-docs/1.7/index.md`.
- The table of contents of each page is automatically generated based on the top-level headers.
- Directory tables of contents are automatically generated based on `post_title` and `post_excerpt` headers.

### Style guidelines

- Use active voice wherever possible, which tells who or what is performing the action.
- Use sentence-style capitalization for headings in most cases.

### Make your update

1. Create a [new issue](https://github.com/mesosphere/dcos-docs-enterprise/issues) in this repo (`dcos-docs-enterprise`).
1. [Fork](https://help.github.com/articles/fork-a-repo/) this repo, `dcos-docs-enterprise` (you only have to do this once).
1. Create your content. In most cases you should be able to create your content within the existing directory structure. 

    - To create a single page.
      
      1.  Create a markdown file `{post_slug}.md` where `post_slug` is your file name. File names become URIs. If you would like this page to be a child of another page, place the `.md` file in the folder for that parent page.
          Add the default metadata, Wordpress will fill in the rest once the page is added. Do not include any other metadata at this point.
          
              ---
              post_title: The Title
              layout: page
              published: true
              ---
              Post markdown goes here.
      
    - To create a page with hierarchy, you must create a top-level parent page and a nested folder for the child pages. The parent page resides outside of the nested folder. 
    
      1.  From your home directory, create a markdown file `{post_slug}.md` where `post_slug` is the slug (final uri segment) you want your parent page to have (e.g. `/home/kittens.md`).
              
      1.  From your home directory, create a folder that has the same name as the file created in step 1 (e.g. `/home/kittens/`). This folder is for the child pages. 
    
      1.  From the child page directory, create the markdown file `{post_slug}.md` where `post_slug` is the slug (final uri segment) you want your page to have (e.g. `/home/kittens.md`).
    
1. Add images in the central image repository: `/assets/images`.

    
### Submit a pull request

1. When you're done, submit a [pull request](https://help.github.com/articles/using-pull-requests/) to `dcos-docs-enterprise`.
