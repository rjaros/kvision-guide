# Migration

This is the list of incompatibilities you may encounter when migrating your application from KVision 1.

* The default font size in Bootstrap 4 is larger. You may have to adjust your application layout manually or use a [custom CSS theme](themes.md).
* The direct child of the `Root` component is no longer 100% wide. Use `width = 100.perc` if you want it to be.
* The modules names have changed \(the API of the components, package names, class names, methods and properties are mostly the same, with the differences described in details below\).

| KVision 1 module name | KVision 2 module name |
| :--- | :--- |
| kvision-select | kvision-bootstrap-select |
| kvision-select-remote | kvision-bootstrap-select-remote |
| kvision-datetime | kvision-bootstrap-datetime |
| kvision-spinner | kvision-bootstrap-spinner |
| kvision-upload | kvision-bootstrap-upload |
| kvision-dialog | kvision-bootstrap-dialog |

* Two new modules: `kvision-bootstrap-css` i `kvision-fontawesome` were extracted from the `kvision-bootstrap` module.
* The Glyphicons support is dropped. Use Font Awesome icons instead.
* The Font Awesome icon names require a style prefix \(`fas`, `far` or `fab`\).
* Components like dropdown, context menu, modal, window, progressbar, navbar, toolbar, responsive grid, tab panel, tooltip and popover were moved into `kvision-bootstrap` module.
* `ButtonStyle.DEFAULT` was removed. The `ButtonStyle.PRIMARY` value is the new default. New `ButtonStyle.SECONDARY` value is also available.
* `RadioStyle.DEFAULT` and `CheckBoxStyle.DEFAULT` were removed and are no longer necessary.
* The deprecated `Label` component was removed. Use `Span` component instead.
* `TableType.CONDENSED` was removed. Use new `TableType.SMALL` value instead.
* `ButtonGroupStyle.JUSTIFIED` was removed. Use a [different approach](https://getbootstrap.com/docs/4.0/migration/#button-group).
* The `responsive` property of the `Table` component was removed. Use `responsiveType` property with a chosen enum value instead.
* The `dropup` property of the `DropDown` component was removed. Use the`direction` property with a `Direction.DROPUP` value instead.
* The `withCaret` parameter of the `DropDown` component constructor was removed. No replacement at the moment.
* `NavbarType.STATICTOP` was removed. Use `NavbarType.STICKYTOP` instead.
* Do not use `Tag(TAG.LI)` components when creating links inside `Nav` component. Use `Link` with `nav-item` and `nav-link` classes \(or use `navLink` DSL builder function\).
* When adding links to the `DropDown` or `ContextMenu` use `Link` with a `dropdown-item` class \(or use `ddLink` and `cmLink` DSL builder functions respectively\).
* `GridSize.XS` was removed from the `ResponsiveGridPanel` component.
* The `buttonsType` is no longer a var property in the `Spinner` and the `SpinnerInput` components \(changing its value didn't work anyway ;-\)
* The `DateTime` and `DateTimeInput` components are based on a new version of bootstrap-datetimepicker. The `weekstart`, `todayhighlight` and`showmeridian` properties were removed. The `todayBtn` and `clearBtn` properties were renamed to `showTodayButton` and `showClear` respectively. 

