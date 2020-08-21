# Modules

KVision consists of both required and optional functionality. Modules can be added as dependencies in `build.gradle.kts` file. This is the current list of available modules.

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
        <p><em>See</em>  <a href="themes.md"><em>Theming</em></a>  <em>chapter for more information.</em>
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
      <td style="text-align:left">kvision-chart</td>
      <td style="text-align:left"><a href="../part-2-advanced-features/charts.md">Chart</a> component.</td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-datacontainer</td>
      <td style="text-align:left"><a href="../part-2-advanced-features/observable-data-model.md">DataContainer</a> component.</td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-dialog</td>
      <td style="text-align:left"><a href="windows-and-modals.md#dialog-with-a-result">Dialog</a> component.</td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-tabulator</td>
      <td style="text-align:left"><a href="../part-2-advanced-features/tabulator-tables.md">Tabulator</a> component.</td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-moment</td>
      <td style="text-align:left">Kotlin language bindings for <a href="https://momentjs.com/">Moment.js</a> library.</td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-pace</td>
      <td style="text-align:left"><a href="https://github.hubspot.com/pace/docs/welcome/">Pace</a> automatic
        page loader.</td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-redux</td>
      <td style="text-align:left">
        <p><a href="https://redux.js.org/">Redux</a> state container.</p>
        <p>See <a href="../part-2-advanced-features/using-redux.md">Using Redux</a> chapter
          for more information.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-redux-kotlin</td>
      <td style="text-align:left">
        <p><a href="https://reduxkotlin.org/">ReduxKotlin</a> state container.</p>
        <p>See <a href="../part-2-advanced-features/using-redux.md">Using Redux</a> chapter
          for more information.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-cordova</td>
      <td style="text-align:left">
        <p>Kotlin language bindings for Apache Cordova core API.</p>
        <p>See <a href="../part-2-advanced-features/building-with-apache-cordova.md">Building with Apache Cordova</a> chapter
          for more information.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-electron</td>
      <td style="text-align:left">
        <p>Kotlin language bindings for Electron API.</p>
        <p><em>See</em>  <a href="../part-2-advanced-features/building-with-electron.md"><em>Building with Electron</em></a>  <em>chapter for more information.</em>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-remote</td>
      <td style="text-align:left">
        <p>KVision JS module for multi-platform, full-stack applications.</p>
        <p><em>See:</em>  <a href="https://kvision.gitbook.io/kvision-guide/part-3-server-side-interface"><em>Part 3</em></a><em>&#x200B;</em>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-select-remote</td>
      <td style="text-align:left">
        <p>Select form component tailored for multi-platform, full-stack applications.</p>
        <p><em>See:</em>  <a href="https://kvision.gitbook.io/kvision-guide/part-3-server-side-interface"><em>Part 3</em></a><em>&#x200B;</em>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-tabulator-remote</td>
      <td style="text-align:left">
        <p>Tabulator component tailored for multi-platform, full-stack applications.</p>
        <p>See: <a href="../part-3-server-side-interface/">Part 3</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-common-types</td>
      <td style="text-align:left">
        <p>KVision common module for multi-platform, full-stack applications, with
          types definitions.</p>
        <p><em>See:</em>  <a href="https://kvision.gitbook.io/kvision-guide/part-3-server-side-interface"><em>Part 3</em></a><em>&#x200B;</em>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-common-remote</td>
      <td style="text-align:left">
        <p>KVision common module for multi-platform, full-stack applications, with
          remote services definitions.</p>
        <p><em>See:</em>  <a href="https://kvision.gitbook.io/kvision-guide/part-3-server-side-interface"><em>Part 3</em></a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-server-ktor</td>
      <td style="text-align:left">
        <p><a href="https://ktor.io/">Ktor</a> server-side connectivity module for
          multi-platform, full-stack applications.</p>
        <p><em>See:</em>  <a href="../part-3-server-side-interface/"><em>Part 3</em></a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-server-jooby</td>
      <td style="text-align:left">
        <p><a href="https://jooby.org">Jooby</a> server-side connectivity module for
          multi-platform, full-stack applications.</p>
        <p><em>See:</em>  <a href="../part-3-server-side-interface/"><em>Part 3</em></a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-server-spring-boot</td>
      <td style="text-align:left">
        <p><a href="https://spring.io/projects/spring-boot">Spring Boot</a> server-side
          connectivity module for multi-platform, full-stack applications.</p>
        <p><em>See:</em>  <a href="../part-3-server-side-interface/"><em>Part 3</em></a>
        </p>
      </td>
    </tr>
  </tbody>
</table>

