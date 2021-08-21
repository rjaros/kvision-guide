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
        <p>Bootstrap based components.</p>
        <p>Can be omitted for applications, which use only the core functionality
          of the framework.</p>
        <p><em>See</em>  <a href="themes.md"><em>Theming</em></a>  <em>chapter for more information.</em>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-bootstrap-css</td>
      <td style="text-align:left">
        <p>Standard Bootstrap CSS styling.</p>
        <p>Can be omitted for applications, which use external Bootstrap CSS.</p>
        <p><em>See</em>  <a href="themes.md"><em>Theming</em></a>  <em>chapter for more information.</em>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-bootstrap-select</td>
      <td style="text-align:left">Bootstrap based advanced select form component.</td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-bootstrap-datetime</td>
      <td style="text-align:left">Bootstrap based date and time picker form components.</td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-bootstrap-spinner</td>
      <td style="text-align:left">Bootstrap based spinner form component.</td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-bootstrap-upload</td>
      <td style="text-align:left">Bootstrap based upload form component.</td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-bootstrap-typeahead</td>
      <td style="text-align:left">Bootstrap based typeahead (autocomplete) form component.</td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-bootstrap-dialog</td>
      <td style="text-align:left">Bootstrap based <a href="windows-and-modals.md#dialog-with-a-result">Dialog</a> component.</td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-bootstrap-icons</td>
      <td style="text-align:left"><a href="https://icons.getbootstrap.com/">Bootstrap Icons</a> support.</td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-fontawesome</td>
      <td style="text-align:left"><a href="https://fontawesome.com">Font Awesome</a> support.</td>
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
      <td style="text-align:left">kvision-richtext</td>
      <td style="text-align:left">Rich text form component.</td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-chart</td>
      <td style="text-align:left"><a href="../part-2-advanced-features/charts.md">Chart</a> component.</td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-datacontainer</td>
      <td style="text-align:left"><a href="../part-2-advanced-features/data-container.md">DataContainer</a> component.</td>
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
      <td style="text-align:left"><a href="https://codebyzach.github.io/pace/">Pace</a> automatic page loader.</td>
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
      <td style="text-align:left">kvision-maps</td>
      <td style="text-align:left">
        <p>A basic module with the <code>Maps</code> component, based on <a href="https://leafletjs.com/">Leaflet</a> library.</p>
        <p>PR welcomed!</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-toast</td>
      <td style="text-align:left">Toast messages.</td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-print</td>
      <td style="text-align:left">Printing support with <a href="https://printjs.crabbly.com/">Print.js</a> library.</td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-routing-navigo</td>
      <td style="text-align:left">Routing module based on <a href="https://github.com/krasimir/navigo/blob/master/README_v7.md">Navigo 7</a> library.</td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-routing-navigo-ng</td>
      <td style="text-align:left">Routing module based on <a href="https://github.com/krasimir/navigo">Navigo 8+</a> library.</td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-onsenui</td>
      <td style="text-align:left"><a href="https://onsen.io/">Onsen UI</a> mobile web components.</td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-jquery</td>
      <td style="text-align:left"><a href="https://jquery.com/">jQuery</a> bindings, events and animations.</td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-rest</td>
      <td style="text-align:left">Configurable REST/HTTP client.</td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-state</td>
      <td style="text-align:left">State bindings and observable data structures.</td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-state-flow</td>
      <td style="text-align:left">Extensions for Kotlin coroutines <code>Flow</code>, <code>StateFlow</code> and <code>SharedFlow</code>.</td>
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
      <td style="text-align:left">kvision-bootstrap-select-remote</td>
      <td style="text-align:left">
        <p>Bootstrap based select form component tailored for full-stack applications.</p>
        <p><em>See:</em>  <a href="https://kvision.gitbook.io/kvision-guide/part-3-server-side-interface"><em>Part 3</em></a><em>&#x200B;</em>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-bootstrap-typeahead-remote</td>
      <td style="text-align:left">
        <p>Bootstrap based typeahead (autocomplete) form component tailored for full-stack
          applications.</p>
        <p><em>See:</em>  <a href="https://kvision.gitbook.io/kvision-guide/part-3-server-side-interface"><em>Part 3</em></a><em>&#x200B;</em>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-tabulator-remote</td>
      <td style="text-align:left">
        <p>Tabulator component tailored for full-stack applications.</p>
        <p>See: <a href="../part-3-server-side-interface/">Part 3</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-common-annotations</td>
      <td style="text-align:left">Compiler plugin annotations for full-stack applications.
        <br /><em>See:</em>  <a href="https://kvision.gitbook.io/kvision-guide/part-3-server-side-interface"><em>Part 3</em></a><em>&#x200B;</em>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-common-types</td>
      <td style="text-align:left">
        <p>KVision common module for full-stack applications, with types definitions.</p>
        <p><em>See:</em>  <a href="https://kvision.gitbook.io/kvision-guide/part-3-server-side-interface"><em>Part 3</em></a><em>&#x200B;</em>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-common-remote</td>
      <td style="text-align:left">
        <p>KVision common module for full-stack applications, with remote services
          definitions.</p>
        <p><em>See:</em>  <a href="https://kvision.gitbook.io/kvision-guide/part-3-server-side-interface"><em>Part 3</em></a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-server-ktor</td>
      <td style="text-align:left">
        <p><a href="https://ktor.io/">Ktor</a> server-side connectivity module for
          full-stack applications.</p>
        <p><em>See:</em>  <a href="../part-3-server-side-interface/"><em>Part 3</em></a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-server-jooby</td>
      <td style="text-align:left">
        <p><a href="https://jooby.io">Jooby</a> server-side connectivity module for
          full-stack applications.</p>
        <p><em>See:</em>  <a href="../part-3-server-side-interface/"><em>Part 3</em></a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-server-spring-boot</td>
      <td style="text-align:left">
        <p><a href="https://spring.io/projects/spring-boot">Spring Boot</a> server-side
          connectivity module for full-stack applications.</p>
        <p><em>See:</em>  <a href="../part-3-server-side-interface/"><em>Part 3</em></a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-server-javalin</td>
      <td style="text-align:left">
        <p><a href="https://javalin.io">Javalin</a> server-side connectivity module
          for full-stack applications.</p>
        <p><em>See:</em>  <a href="../part-3-server-side-interface/"><em>Part 3</em></a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-server-vertx</td>
      <td style="text-align:left">
        <p><a href="https://vertx.io">Vert.x</a> server-side connectivity module for
          full-stack applications.</p>
        <p><em>See:</em>  <a href="../part-3-server-side-interface/"><em>Part 3</em></a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">kvision-server-micronaut</td>
      <td style="text-align:left">
        <p><a href="https://micronaut.io">Micronaut</a> server-side connectivity module
          for full-stack applications.</p>
        <p><em>See:</em>  <a href="../part-3-server-side-interface/"><em>Part </em></a>
        </p>
      </td>
    </tr>
  </tbody>
</table>

