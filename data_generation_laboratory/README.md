### Define data generation laboratory 

Data generation laboratory is the directory where all the data mining
and cleaning code resides. It shall include, data scraping scripts, data
cleaning code etc.

Large files themselves are not hosted here. They should be hosted
somewhere else, like [zenodo](https://zenodo.org).

Each node (folder) in this directory should contain files in the format:

```
node ---|
        |
        |---> input/*
        |
        |---> notebook.ipynb
        |
        |---> output/*
```

You notebook, shall download the data from zenodo, and change the data
to produce outputs. All outputs shall be saved in `output/*`

The reason, why such a configuration is adopted is because github does
not allow to store large files. And large files are also difficult to
manage using git. 

So the idea is, keep code files in this repository , keep the large
files in `zenodo`. Use [zenodo REST API](https://developers.zenodo.org/)
to download and upload files remotely on the fly. As required. 
