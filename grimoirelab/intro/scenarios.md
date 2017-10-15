## GrimoireLab scenarios

GrimoireLab is modular not only in the sense that it is composed by modules, but also in the sense that modules can be use separately. To illustrate this, let's visit some different scenarios of how GrimoireLab can be used to analyze software development, from simpler ones, to some of the most complex.

### Perceval analyzing a single data source

In this case, we will use just Perceval, as a Python module, to retrieve information from one data source. For example, that can be a git repository, a GitHub project, or a mailing list.

![](/grimoirelab/grimoirelab-fit-perceval-1.png)

As shown in the figure above, data retrieved by Perceval is consumed by a Python script, which produces, for example, a summary of the data in the repository. In this simple scenario, most of the components in GrimoireLab are not needed, and the setup is rather simple: just the Perceval Python module needs to be installed. This is the case explained in sections [Retriving git metadata](/training/perceval/git.md), [Retrieving data from GitHub repositories](/training/perceval/github.md) and [Mail archives](/training/perceval/mail.md).

### Perceval analyzing several data sources

The scenario can be a bit more complex when we need to do a simple analysis of several data sources. We can still retrieve data with Perceval, which will be called from our script.

![](/grimoirelab/grimoirelab-fit-perceval-n.png)

In fact, for each of the data sources, several repositories can be retrieved and analyzed. Therefore, this scenario can in fact become very complex, with a lot of analytics being performed by the Python script.

### Producing raw indexes

The above scenario is affected by a problem: every time you want to re-run the Python script, Perceval nees to access the data sources once again. This may be a problem if the data retrieval process is long, error-prone, or stresses too much the infrastructure where the data resides.

To avoid these problems, GrimoireLab allows for an scenario where data sources are retrieved only once: store all the retrieved data in ElasticSearch indexes. This is one of the functionalities provided by GrimoireELK: run Perceval to access data, and store it in one raw index per data source.

![](/grimoirelab/grimoirelab-fit-grimoireelk-raw.png)

Now, the script just queries the ElasticSearch raw indexes, producing the same information that would be obtained if getting once and again the data from the original data sources. But once the data was retrieved, the script will be much faster, since it just queries the database instead of retrieving data from its origins. In addition, results are reproducible, as long as the indexes don't change. And of course, the infrastructure providing the data sources are not stressed because of our repeated analysis.

The main drawback of this approach is that the next time we run the script, maybe the data in the original data source changed. For example, more commits were merged, or new tickets were open, closed, or commented. If we want to do the analysis as we would do in the original data source, we need to retrieve the new data, and store it in ElasticSearch. Fortunately, GrimoreELK has incremental capabilities for satisfying this need. If we want, we can run it incremental mode, which will drive GrimoireELK to use Perceval to retrieve only new data since when the original data source was accessed.

This means that we can use this scenario to retrieve data once, and do reproducible analysis on the ElasticSearch indexes, or to retrieve it incrementally, having up-to-date indexes. But still, the analysis will be much faster than retrieving all the data again from its origins, and will stress much less the original infrastructure.