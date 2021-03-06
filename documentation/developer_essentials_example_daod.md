Configuring the "data access on-demand" example project
=======================================================

The "data access on-demand" approach to data acquisition in i2 Analyze involves querying external data through a new service. In Developer Essentials, IBM provides the `da-subset-filesystem-example` example, in which the external data source is an XML file.

Before you begin
----------------

The data access on-demand example project requires the development version of i2 Analyze, prepared according to the Developer Essentials deployment instructions. However, the example has no additional requirements.

Note: All the data acquisition example projects in Developer Essentials use the same XML data, and the same code for transforming and converting that data into items in the Analysis Repository. These artifacts are in the `SDK\sdk-projects\da-example-common` directory, to which all of the data acquisition examples have access.

About this task
---------------

The data access on-demand example defines and configures services that provide users with access to an external data source through the Intelligence Portal. Configuring the example requires you to add the data source to the existing i2 Analyze deployment, and to add the services to the platform.

Procedure
---------

The examples in Developer Essentials require links to the i2 Analyze libraries, and to the API documentation. These links are configured in a separate project directory that you must import into Eclipse before you can build any of the example projects.

1.  If you did not already add the `master` directory and the related artifacts to your Eclipse workspace while you worked with one of the other examples, add it now.
    a.  In Eclipse, click **Window** &gt; **Preferences** to open the Preferences window.
    b.  In the Preferences window, select **General** &gt; **Workspace** &gt; **Linked Resources**.
    c.  Add an entry to the **Defined path variables** list:

        -   Name: `TOOLKIT_ROOT`
        -   Location: `C:\IBM\i2analyze\SDK\sdk-projects\master\build\toolkit`

        The shared libraries and several of the example projects rely on the presence of `TOOLKIT_ROOT` in your development environment.

    d.  In Eclipse, click **File** &gt; **Import** to open the Import window.
    e.  In the Import window, click **General** &gt; **Existing Projects into Workspace**, and then click **Next**.
    f.  Click **Browse** at the top of the window, and then select the `C:\IBM\i2analyze\SDK\sdk-projects\master` directory.
    g.  Click **Finish** to complete the import process.

2.  Repeat steps 1d to 1g to import `C:\IBM\i2analyze\SDK\sdk-projects\da-example-common` into Eclipse.

By its nature, the data access on-demand example requires you to modify your deployment of the platform. The scripts in the deployment toolkit can change `topology.xml` automatically. You must then redeploy the platform by hand.

1.  If the development version of the `i2analyze` server is running, stop the server from Eclipse.
2.  Open a command prompt as Administrator, and navigate to the `C:\IBM\i2analyze\SDK\sdk-projects\master` directory.
3.  Run the following command:

    ``` pre
    build -pr da-subset-filesystem-example -t addDaodDataSource
    ```

    Important: For successful integration with Eclipse, the value that you provide to the `-pr` option must match the name of the example project.

    When the command runs successfully, it modifies the topology file in your development version of i2 Analyze to add information about a new data source. By default, the data source gets the same name as the value that you provide to the `-pr` option, but you can modify the name later if you want to.

    The command also creates a Java library that maps from platform-compatible XML to Analysis Repository items. Unlike in the "data load direct" project, the `addDaodDataSource` target of the `build` command automatically determines what schema the deployed platform is using, and performs the necessary configuration.

4.  Open the topology file that represents your deployment of i2 Analyze in an XML editor. By default, this file is at `C:\IBM\i2analyze\SDK\sdk-projects\master\build\toolkit\configuration\environment\topology.xml`.
5.  Edit the `<i2-data-source>` element that defines the data access on-demand data source so that its attributes reflect the functionality of the example:

    ``` pre
    <i2-data-source id="daod1" ar="false">
       <DataSource ScsSearchSupported="true" EdrsGetContextSupported="false" 
                   ScsNetworkSearchSupported="false" EdrsGetLatestItemsSupported="false"  
                   Version="0" ScsPresent="true" ScsFilteredSearchSupported="false" 
                   ScsBrowseSupported="true" Id="" ScsDumbbellSearchSupported="false" 
                   SesPresent="true" EdrsPresent="false"
                   >
          <Shape>DAOD</Shape>
          <Name>da-subset-filesystem-example</Name>
       </DataSource>
    </i2-data-source>
    ```

    Note: The first data access on-demand data source that you add to the topology file gets the identifier `daod1`. Later additions receive different identifiers.

6.  To redeploy i2 Analyze, run the following command:

    ``` pre
    build -t deploy
    ```

7.  Use the Services application in Windows (`services.msc`) to restart IBM HTTP Server. Do not attempt to start the application server yet.

The commands so far deployed the example project application from the deployment toolkit. To enable easy debugging of your code, the next steps are all about replacing the application that you deployed from the toolkit with code from Eclipse.

1.  Refresh the Eclipse user interface to ensure that it reflects the current state of the project.
2.  In your Eclipse workspace, open the `WebSphere Application Server Liberty/servers/i2analyze/apps` folder, and delete the `da-subset-filesystem-example.war` application.
3.  Repeat the instructions that you followed when you added the `master` directory to your Eclipse workspace. This time, add the `C:\IBM\i2analyze\SDK\sdk-projects\da-subset-filesystem-example` directory to Eclipse.
4.  Ensure that the JAR file from `da-example-common` is loaded correctly into the deployment assembly for the `da-subset-filesystem-example` project:
    a.  In Package Explorer, right-click **da-subset-filesystem-example**, and select **Properties** to display the Properties window.
    b.  Click **Deployment Assembly**, and look for any error messages about `da-example-common`.
    c.  If there are error messages, remove `da-example-common` from the packaging structure, and then use **Add** &gt; **Project** to add it again.

5.  In the **Servers** tab at the bottom of the Eclipse application window, right-click the **i2analyze** server and select **Add and Remove**.
6.  In the Add and Remove window, move **da-subset-filesystem-example** from the **Available** list to the **Configured** list, and then click **Finish** to close the window.

At this stage, deployment of the data access on-demand example is complete. You can start i2 Analyze, and connect to it through the Intelligence Portal.

1.  In Eclipse, start the `i2analyze` server.
2.  In a web browser, load or refresh your view of the Intelligence Portal. Browse the **da-subset-filesystem-example** data source to see that the items to which it provides access are available to the user.

After you develop and test a data access on-demand solution for i2 Analyze, the next step is to publish it to a live deployment.

These instructions assume that you have access to two instances of i2 Analyze:

-   The development version of the platform, deployed according to the instructions in IBM i2 Analyze Developer Essentials. This instance contains the custom application that you want to publish.
-   The production version of the platform, deployed according to the instructions in the IBM i2 Analyze deployment documentation. This instance has all of its default settings.

1.  Copy `C:\IBM\i2analyze\SDK\sdk-projects\master\build\toolkit\configuration` from the development version into the production version of the deployment toolkit.
2.  Edit the following files to ensure that the default settings match your environment:
    -   `configuration\environment\i2analyze\environment.properties`
    -   `configuration\environment\http-server.properties`
    -   `configuration\environment\credentials.properties`
    -   `configuration\environment\topology.xml`

3.  On the production version of the platform, open a command prompt as Administrator, and navigate to the `toolkit\scripts` directory.
4.  To redeploy the platform, run the following command:

    ``` pre
    setup -t deploy
    ```

5.  Use the Services application in Windows (`services.msc`) to restart IBM HTTP Server.

**Parent topic:** <a href="developer_essentials_welcome.md" class="link" title="IBM i2 Analyze Developer Essentials contains tools, libraries, and examples that enable development and deployment of custom extensions to i2 Analyze.">IBM i2 Analyze Developer Essentials</a>

------------------------------------------------------------------------

© Copyright IBM Corporation 2014, 2016.


