# Modules

KVision consists of both required and optional functionality. Modules can be added as dependencies in `build.gradle` file. This is the current list of available modules.

<table>
  <thead>
    <tr>
      <th style="text-align:left">Module</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">kvision</td>
      <td style="text-align:left">Core module required for all applications.</td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-bootstrap</td>
      <td style="text-align:left">
        <p>Bootstrap styles, layouts and components.</p>
        <p>Can be omitted for applications, which use only the base functionality
          of the framework.</p>
        <p><em>Note: A number of core and optional components do not work correctly without this module.</em>
        </p>
        <p><em>See </em><a href="themes.md"><em>Theming</em></a><em> chapter for more information.</em>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-handlebars</td>
      <td style="text-align:left"><a href="https://handlebarsjs.com/">Handlebars.js</a> templates support
        for text components.</td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-i18n</td>
      <td style="text-align:left">Internationalization support.</td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-select</td>
      <td style="text-align:left">Select form component.</td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-datetime</td>
      <td style="text-align:left">Date and time form components.</td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-spinner</td>
      <td style="text-align:left">Spinner form component.</td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-richtext</td>
      <td style="text-align:left">Rich text form component.</td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-upload</td>
      <td style="text-align:left">Upload form component.</td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-remote</td>
      <td style="text-align:left">
        <p>KVision JS module for multi-platform, full-stack applications.</p>
        <p><em>See:</em>  <a href="https://kvision.gitbook.io/kvision-guide/~/drafts/-LQzzS6ee8cG6vfpNbeb/primary/part-3-server-side-interface"><em>Part 3</em></a><em>​</em>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-select-remote</td>
      <td style="text-align:left">
        <p>Select form component tailored for multi-platform, full-stack applications.</p>
        <p><em>See:</em>  <a href="https://kvision.gitbook.io/kvision-guide/~/drafts/-LQzzS6ee8cG6vfpNbeb/primary/part-3-server-side-interface"><em>Part 3</em></a><em>​</em>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-common-types</td>
      <td style="text-align:left">
        <p>KVision common module for multi-platform, full-stack applications, with
          types definitions.</p>
        <p><em>See:</em>  <a href="https://kvision.gitbook.io/kvision-guide/~/drafts/-LQzzS6ee8cG6vfpNbeb/primary/part-3-server-side-interface"><em>Part 3</em></a><em>​</em>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-common-remote</td>
      <td style="text-align:left">
        <p>KVision common module for multi-platform, full-stack applications, with
          remote services definitions.</p>
        <p><em>See:</em>  <a href="https://kvision.gitbook.io/kvision-guide/~/drafts/-LQzzS6ee8cG6vfpNbeb/primary/part-3-server-side-interface"><em>Part 3</em></a><em></em>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-server-jooby</td>
      <td style="text-align:left">
        <p><a href="https://jooby.org">Jooby</a> server-side connectivity module for
          multi-platform, full-stack applications.</p>
        <p><em>See: </em><a href="../part-3-server-side-interface.md"><em>Part 3</em></a><em></em>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-server-spring-boot</td>
      <td style="text-align:left">
        <p><a href="https://spring.io/projects/spring-boot">Spring Boot</a> server-side
          connectivity module for multi-platform, full-stack applications.</p>
        <p><em>See: </em><a href="../part-3-server-side-interface.md"><em>Part 3</em></a><em></em>
        </p>
      </td>
    </tr>
  </tbody>
</table>Add to your application only modules which are used. By removing unused modules you will get faster build times and much smaller size of JavaScript output code.

