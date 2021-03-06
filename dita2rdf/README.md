Thanks for giving a spin to the dita2rdf plugin! Please report bugs and questions to https://github.com/ColinMaudry/dita-rdf/issues.

###What does it do?

The dita2rdf DITA Open Toolkit plugin (aka the dita2rdf plugin) adds a new transtype to the DITA OT: rdf.

In short, running the rdf transtype on a DITA map or topic runs an extraction of the metadata of the content and stores it in RDF/XML format, a serialization of RDF. The transformation not only extracts the metadata of the input file, it also follows the references (@href and resolved @keyref) to cover the whole documentation set.

"Why on Earth would I want to do that?". Good question. I will give you great reasons when I add an extra step to the transformation: the upload of the result to a triple store, an RDF data base. I might also speak about it during the next DITA Europe conference if the committee considers these great reasons great enough.

###Requirements

* [DITA open toolkit](http://dita-ot.github.io/) 1.8.x (version 1.5+ could work but not tested)

###Installation

1. Extract `/dita2rdf/dita2rdf-ditaot-plugin.zip` to `[DITA-OT]/plugins`, `[DITA-OT]` being the directory of your DITA open toolkit installation.
2. Run `[DITA-OT]/startcmd.bat` (or `[DITA-OT]/startcmd.sh` if you run a Linux operating system)
3. Run the command `ant -f integrator.xml strict` at the root of your DITA opent toolkit installation.

The plugin is installed and the new `rdf` transtype is available.

###Configuration (optional for a first run, recommended otherwise)

1. The plugin must be installed in your DITA OT
2. In `/plugins/com.github.colinmaudry.dita2rdf/customization`, make a copy of `catalog.xml.orig`
3. Rename the copy `catalog.xml`
3. In `catalog.xml`, uncomment `<uri name="cfg:rdf/config.xml" uri="rdf/config.xml"/>`
4. Save
4. In `/plugins/com.github.colinmaudry.dita2rdf/customization/rdf`, make a copy of `config.xml.orig`
5. Rename the copy `config.xml`
4. In `config.xml`, follow the instructions to edit the configuration
5. Save

###Test

1. In `[DITA-OT]`, run `ant -Dargs.input=docsrc/userguide.ditamap -Dtranstype=rdf`
2. Report issues and comments [here](https://github.com/ColinMaudry/dita-rdf/issues)

The DITA-OT extracts the metadata of the DITA source files of the DITA-OT user guide. When finished, the resulting `userguide.rdf` is available in `[DITA-OT]/out`.

###Extracting RDF for your own DITA map

1. In `[DITA-OT]`, run `ant -Dargs.input=[path/to/your/ditamap] -Doutput.dir=out -Dtranstype=rdf`

The DITA-OT extracts the metadata of the provided DITA map. When finished, the resulting `[map name].rdf` is available in `[DITA-OT]/out`. Depending on your source DITA folder structure, the resulting RDF can also be in a sub-directory of `[DITA-OT]/out`. Check the last lines of the processing log ([xslt]).

###Known issues

* https://github.com/ColinMaudry/dita-rdf/labels/bug

### You should keep reading

This plugin enables you to export the metadata of a DITA map and the referenced topics in RDF, via an RDF/XML serialization.

Because the development of this plugin started in late-2013, it exclusively uses XSLT 2.0 and consequently only works with Saxon 9.x+ and other XSLT 2.0 compliant processors.

In order to keep the plugin light-weight, with good performance and low code maintenance, I have left the "data safeguards" to a minimum. What is that? It means that this plugin applies the saying "rubbish in, rubbish out". To be more specific, it means that if the metadata contained in your DITA content is inconsistent, incomplete or simply wrong, it will not be easy to use the resulting RDF.

To avoid issues, here are a couple of things you can do to have good metadata output: 
- make sure all the maps and topics have an @id attribute in the root element, and that it is unique.
- make sure all the maps and topics have a @xml:lang attribute in the root element that respects [the recommendations of the W3C](http://www.w3.org/International/articles/language-tags/). Due to certain spelling and cultural variants (e.g. date formats), distinguishing Bristish English (en-UK) and American English (en-US) is recommended.
- make sure your topicref have a @format attribute with [the right value](http://docs.oasis-open.org/dita/v1.2/os/spec/common/theformatattribute.html). It can go well without, looking for '.dita' in the @href value, but some URL might abuse this rule (eg. @href="http://blog.dita.xml.org").

###Under the hood

Here is the part dedicated to satisfy my fellow DITA hackers.

The DITA RDF plugin was developped reusing the methodology of the pdf2 plugin for the build and the customization, as I thought many DITA-OT users would feel more comfortable with a familiar structure.

However, the dita2rdf plugin differs on a couple of points:

- The preprocess is 'specialized' to remove the steps that are not necessary for the purpose of this plugin. It also speeded up the processing. More details [here](https://github.com/ColinMaudry/dita-rdf/issues/14).
- Though the original intent was to rely on the topicmerge to flatten the structure and have only file to process, it appeared too much information was left behind: mapref and @processing-role="resource-only". Topicmerge was consequently also removed, and the parsing is done by following the @href to jump from file to file (the temp files, not the original ones).

For the long term, I'll aim at using as much XSLT as possible to make the code more portable. Possibly with a compatibility starting with DITA OT 2.0.
