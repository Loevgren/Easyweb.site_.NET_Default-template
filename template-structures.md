Här är template-strukturen för vyn Layout:
```json
{
  "view": {
    "viewKey": "Layout",
    "typeKey": "ViewType.Layout",
    "label": "Layout view",
    "moduleKey": "Layout",
    "moduleId": 8
  },
  "templateTree": {
    "typeKey": "ViewType.Layout",
    "label": "Layout view",
    "description": "",
    "noInput": true,
    "contentLimit": null,
    "orderBy": null,
    "children": [
      {
        "typeKey": "SectionType.Header",
        "label": "Header",
        "description": "Toppsektionen för alla sidor",
        "single": true,
        "contentLimit": 1,
        "orderBy": null,
        "children": [
          {
            "customKey": "logotype",
            "typeKey": "ReferenceType.Image",
            "label": "Logo",
            "single": true,
            "contentLimit": 1,
            "orderBy": null
          },
          {
            "typeKey": "FeatureType.Menu",
            "label": "Main menu",
            "description": "",
            "noInput": true,
            "contentLimit": null,
            "orderBy": 1
          }
        ]
      },
      {
        "typeKey": "SectionType.Article",
        "label": "Page content",
        "description": "",
        "noInput": true,
        "contentLimit": null,
        "orderBy": 1
      },
      {
        "typeKey": "SectionType.Footer",
        "label": "Footer",
        "description": "Bottensektionen för alla sidor",
        "single": true,
        "contentLimit": 1,
        "orderBy": 2,
        "children": [
          {
            "customKey": "logotype",
            "typeKey": "ReferenceType.Image",
            "label": "Logo",
            "single": true,
            "contentLimit": 1,
            "orderBy": null
          },
          {
            "typeKey": "SectionType.Section",
            "label": "Footer text",
            "single": true,
            "contentLimit": 1,
            "orderBy": 1,
            "children": [
              {
                "customKey": "header",
                "typeKey": "DisplayType.TextBoxContent",
                "label": "header",
                "single": true,
                "contentLimit": 1,
                "orderBy": null
              },
              {
                "customKey": "description",
                "typeKey": "DisplayType.TextAreaContent",
                "label": "Description",
                "single": true,
                "contentLimit": 1,
                "orderBy": 1
              }
            ]
          },
          {
            "customKey": "linkBlock",
            "typeKey": "SectionType.Section",
            "label": "Linkblock",
            "isList": true,
            "contentLimit": 3,
            "orderBy": 2,
            "children": [
              {
                "customKey": "links",
                "typeKey": "ReferenceType.Link",
                "label": "Link",
                "isList": true,
                "orderBy": null
              }
            ]
          }
        ]
      }
    ]
  }
}
```

Här är template-strukturen för vyn Home:
```json
{
  "view": {
    "viewKey": "Home",
    "typeKey": "ViewType.Home",
    "label": "Startvy",
    "moduleKey": "Home",
    "moduleId": 1
  },
  "templateTree": {
    "typeKey": "ViewType.Home",
    "label": "Startvy",
    "description": "",
    "noInput": true,
    "contentLimit": null,
    "orderBy": null,
    "children": [
      {
        "customKey": "hero",
        "typeKey": "SectionType.Section",
        "label": "Hero",
        "single": true,
        "contentLimit": 1,
        "orderBy": null,
        "children": [
          {
            "customKey": "header",
            "typeKey": "DisplayType.TextBoxContent",
            "label": "header",
            "single": true,
            "contentLimit": 1,
            "orderBy": null
          },
          {
            "customKey": "description",
            "typeKey": "DisplayType.TextAreaContent",
            "label": "Description",
            "single": true,
            "contentLimit": 1,
            "orderBy": 1
          },
          {
            "customKey": "media",
            "typeKey": "ReferenceType.Media",
            "label": "Media",
            "single": true,
            "contentLimit": 1,
            "orderBy": 2
          },
          {
            "customKey": "link",
            "typeKey": "ReferenceType.Link",
            "label": "Link",
            "single": true,
            "contentLimit": 1,
            "orderBy": 3
          }
        ]
      },
      {
        "customKey": "introduction",
        "typeKey": "SectionType.Section",
        "label": "Introduction",
        "single": true,
        "contentLimit": 1,
        "orderBy": null,
        "children": [
          {
            "customKey": "tag",
            "typeKey": "DisplayType.TextBoxContent",
            "label": "Tag",
            "single": true,
            "contentLimit": 1,
            "orderBy": null
          },
          {
            "customKey": "header",
            "typeKey": "DisplayType.TextBoxContent",
            "label": "Header",
            "single": true,
            "contentLimit": 1,
            "orderBy": 1
          },
          {
            "customKey": "description",
            "typeKey": "DisplayType.TextAreaContent",
            "label": "Description",
            "single": true,
            "contentLimit": 1,
            "orderBy": 2
          },
          {
            "customKey": "link",
            "typeKey": "ReferenceType.Link",
            "label": "Link",
            "single": true,
            "contentLimit": 1,
            "orderBy": 3
          }
        ]
      },
      {
        "customKey": "facts",
        "typeKey": "SectionType.Section",
        "label": "Facts",
        "single": true,
        "contentLimit": 1,
        "orderBy": null,
        "children": [
          {
            "customKey": "header",
            "typeKey": "DisplayType.TextBoxContent",
            "label": "Header",
            "single": true,
            "contentLimit": 1,
            "orderBy": null
          },
          {
            "customKey": "boxes",
            "typeKey": "SectionType.Section",
            "label": "Box",
            "isList": true,
            "orderBy": 1,
            "children": [
              {
                "customKey": "number",
                "typeKey": "DisplayType.TextBoxContent",
                "label": "Number",
                "single": true,
                "contentLimit": 1,
                "orderBy": null
              },
              {
                "customKey": "text",
                "typeKey": "DisplayType.TextBoxContent",
                "label": "Text",
                "single": true,
                "contentLimit": 1,
                "orderBy": 1
              }
            ]
          }
        ]
      },
      {
        "customKey": "textMedia",
        "typeKey": "SectionType.Section",
        "label": "Text & Media",
        "isList": true,
        "orderBy": null,
        "children": [
          {
            "customKey": "reversed",
            "typeKey": "FeatureType.SelectOption",
            "label": "Reveresed",
            "description": "",
            "single": true,
            "contentLimit": 1,
            "orderBy": null
          },
          {
            "customKey": "image",
            "typeKey": "ReferenceType.Image",
            "label": "Image",
            "single": true,
            "contentLimit": 1,
            "orderBy": 1
          },
          {
            "customKey": "tag",
            "typeKey": "DisplayType.TextBoxContent",
            "label": "Tag",
            "single": true,
            "contentLimit": 1,
            "orderBy": 2
          },
          {
            "customKey": "header",
            "typeKey": "DisplayType.TextBoxContent",
            "label": "Header",
            "single": true,
            "contentLimit": 1,
            "orderBy": 3
          },
          {
            "customKey": "description",
            "typeKey": "DisplayType.TextAreaContent",
            "label": "Description",
            "single": true,
            "contentLimit": 1,
            "orderBy": 4
          }
        ]
      },
      {
        "customKey": "banner",
        "typeKey": "SectionType.Section",
        "label": "Banner",
        "single": true,
        "contentLimit": 1,
        "orderBy": null,
        "children": [
          {
            "customKey": "media",
            "typeKey": "ReferenceType.Media",
            "label": "Media",
            "single": true,
            "contentLimit": 1,
            "orderBy": null
          },
          {
            "customKey": "tag",
            "typeKey": "DisplayType.TextBoxContent",
            "label": "Tag",
            "single": true,
            "contentLimit": 1,
            "orderBy": 1
          },
          {
            "customKey": "header",
            "typeKey": "DisplayType.TextBoxContent",
            "label": "Header",
            "single": true,
            "contentLimit": 1,
            "orderBy": 2
          }
        ]
      }
    ]
  }
}
```

Här är template-strukturen för standardvyn i modulen Pages/WebPages:
```json
{
  "view": {
    "viewKey": "Page",
    "typeKey": "ViewType.Page",
    "label": "Sidvy",
    "moduleKey": "Pages",
    "moduleId": 40
  },
  "templateTree": {
    "typeKey": "ViewType.Page",
    "label": "Sidvy",
    "description": "",
    "noInput": true,
    "contentLimit": null,
    "orderBy": null,
    "children": [
      {
        "customKey": "heroPage",
        "typeKey": "SectionType.Section",
        "label": "Hero",
        "single": true,
        "contentLimit": 1,
        "orderBy": null,
        "children": [
          {
            "customKey": "header",
            "typeKey": "DisplayType.TextBoxContent",
            "label": "header",
            "single": true,
            "contentLimit": 1,
            "orderBy": null
          },
          {
            "customKey": "description",
            "typeKey": "DisplayType.TextAreaContent",
            "label": "Description",
            "single": true,
            "contentLimit": 1,
            "orderBy": 1
          },
          {
            "customKey": "media",
            "typeKey": "ReferenceType.Media",
            "label": "Media",
            "single": true,
            "contentLimit": 1,
            "orderBy": 2
          }
        ]
      },
      {
        "typeKey": "SectionType.Flex",
        "label": "Flexsections",
        "isList": true,
        "orderBy": null,
        "children": [
          {
            "customKey": "introduction",
            "typeKey": "SectionType.Section",
            "label": "Introduction",
            "single": true,
            "contentLimit": 1,
            "orderBy": null,
            "children": [
              {
                "customKey": "tag",
                "typeKey": "DisplayType.TextBoxContent",
                "label": "Tag",
                "single": true,
                "contentLimit": 1,
                "orderBy": null
              },
              {
                "customKey": "header",
                "typeKey": "DisplayType.TextBoxContent",
                "label": "Header",
                "single": true,
                "contentLimit": 1,
                "orderBy": 1
              },
              {
                "customKey": "description",
                "typeKey": "DisplayType.TextAreaContent",
                "label": "Description",
                "single": true,
                "contentLimit": 1,
                "orderBy": 2
              },
              {
                "customKey": "link",
                "typeKey": "ReferenceType.Link",
                "label": "Link",
                "single": true,
                "contentLimit": 1,
                "orderBy": 3
              }
            ]
          },
          {
            "customKey": "facts",
            "typeKey": "SectionType.Section",
            "label": "Facts",
            "single": true,
            "contentLimit": 1,
            "orderBy": null,
            "children": [
              {
                "customKey": "header",
                "typeKey": "DisplayType.TextBoxContent",
                "label": "Header",
                "single": true,
                "contentLimit": 1,
                "orderBy": null
              },
              {
                "customKey": "boxes",
                "typeKey": "SectionType.Section",
                "label": "Box",
                "isList": true,
                "orderBy": 1,
                "children": [
                  {
                    "customKey": "number",
                    "typeKey": "DisplayType.TextBoxContent",
                    "label": "Number",
                    "single": true,
                    "contentLimit": 1,
                    "orderBy": null
                  },
                  {
                    "customKey": "text",
                    "typeKey": "DisplayType.TextBoxContent",
                    "label": "Text",
                    "single": true,
                    "contentLimit": 1,
                    "orderBy": 1
                  }
                ]
              }
            ]
          },
          {
            "customKey": "textMedia",
            "typeKey": "SectionType.Section",
            "label": "Text & Media",
            "isList": true,
            "orderBy": null,
            "children": [
              {
                "customKey": "reversed",
                "typeKey": "FeatureType.SelectOption",
                "label": "Reveresed",
                "description": "",
                "single": true,
                "contentLimit": 1,
                "orderBy": null
              },
              {
                "customKey": "image",
                "typeKey": "ReferenceType.Image",
                "label": "Image",
                "single": true,
                "contentLimit": 1,
                "orderBy": 1
              },
              {
                "customKey": "tag",
                "typeKey": "DisplayType.TextBoxContent",
                "label": "Tag",
                "single": true,
                "contentLimit": 1,
                "orderBy": 2
              },
              {
                "customKey": "header",
                "typeKey": "DisplayType.TextBoxContent",
                "label": "Header",
                "single": true,
                "contentLimit": 1,
                "orderBy": 3
              },
              {
                "customKey": "description",
                "typeKey": "DisplayType.TextAreaContent",
                "label": "Description",
                "single": true,
                "contentLimit": 1,
                "orderBy": 4
              }
            ]
          },
          {
            "customKey": "banner",
            "typeKey": "SectionType.Section",
            "label": "Banner",
            "single": true,
            "contentLimit": 1,
            "orderBy": null,
            "children": [
              {
                "customKey": "media",
                "typeKey": "ReferenceType.Media",
                "label": "Media",
                "single": true,
                "contentLimit": 1,
                "orderBy": null
              },
              {
                "customKey": "tag",
                "typeKey": "DisplayType.TextBoxContent",
                "label": "Tag",
                "single": true,
                "contentLimit": 1,
                "orderBy": 1
              },
              {
                "customKey": "header",
                "typeKey": "DisplayType.TextBoxContent",
                "label": "Header",
                "single": true,
                "contentLimit": 1,
                "orderBy": 2
              }
            ]
          },
          {
            "customKey": "textSection",
            "typeKey": "SectionType.Section",
            "label": "Textcontent",
            "single": true,
            "contentLimit": 1,
            "orderBy": null,
            "children": [
              {
                "customKey": "header",
                "typeKey": "DisplayType.TextBoxContent",
                "label": "Header",
                "single": true,
                "contentLimit": 1,
                "orderBy": null
              },
              {
                "customKey": "html",
                "typeKey": "DisplayType.HtmlEditContent",
                "label": "Texteditor",
                "single": true,
                "contentLimit": 1,
                "orderBy": 1
              }
            ]
          },
          {
            "customKey": "teams",
            "typeKey": "SectionType.Section",
            "label": "Teams",
            "single": true,
            "contentLimit": 1,
            "orderBy": null,
            "children": [
              {
                "customKey": "header",
                "typeKey": "DisplayType.TextBoxContent",
                "label": "Header",
                "single": true,
                "contentLimit": 1,
                "orderBy": null
              },
              {
                "customKey": "description",
                "typeKey": "DisplayType.TextAreaContent",
                "label": "Description",
                "single": true,
                "contentLimit": 1,
                "orderBy": 1
              },
              {
                "customKey": "teamMembers",
                "typeKey": "FeatureType.ArticleViewList",
                "label": "Team members",
                "description": "",
                "noInput": true,
                "contentLimit": null,
                "orderBy": 2
              }
            ]
          },
          {
            "customKey": "teams",
            "typeKey": "SectionType.Section",
            "label": "Teams",
            "single": true,
            "contentLimit": 1,
            "orderBy": null,
            "children": [
              {
                "customKey": "header",
                "typeKey": "DisplayType.TextBoxContent",
                "label": "Header above team members",
                "single": true,
                "contentLimit": 1,
                "orderBy": null
              },
              {
                "customKey": "teamMembers",
                "typeKey": "ReferenceType.ArticleBlurb",
                "label": "Team",
                "isList": true,
                "contentLimit": 4,
                "orderBy": 1
              }
            ]
          }
        ]
      },
      {
        "typeKey": "DisplayType.StructuredDataContainer",
        "label": "Strukturerad data (input)",
        "single": true,
        "contentLimit": 1,
        "orderBy": 2
      }
    ]
  }
}
```

Här är template-strukturen för vyn Contact:
```json
{
  "view": {
    "viewKey": "contact",
    "typeKey": "ViewType.Page",
    "label": "contact",
    "moduleKey": "WebPages",
    "moduleId": 40,
    "customKey": "contact"
  },
  "templateTree": {
    "customKey": "contact",
    "typeKey": "ViewType.Page",
    "label": "contact",
    "isList": true,
    "orderBy": null,
    "children": [
      {
        "customKey": "contactDetails",
        "typeKey": "SectionType.Section",
        "label": "Contact details",
        "single": true,
        "contentLimit": 1,
        "orderBy": null,
        "children": [
          {
            "customKey": "header",
            "typeKey": "DisplayType.TextBoxContent",
            "label": "Header",
            "single": true,
            "contentLimit": 1,
            "orderBy": null
          },
          {
            "customKey": "html",
            "typeKey": "DisplayType.HtmlEditContent",
            "label": "Texteditor",
            "single": true,
            "contentLimit": 1,
            "orderBy": 1
          }
        ]
      },
      {
        "customKey": "contactForm",
        "typeKey": "SectionType.Section",
        "label": "Form",
        "noInput": true,
        "contentLimit": null,
        "orderBy": 1,
        "children": [
          {
            "typeKey": "FeatureType.DefaultContactForm",
            "label": "Form",
            "description": "",
            "noInput": true,
            "contentLimit": null,
            "orderBy": null
          }
        ]
      },
      {
        "customKey": "teams",
        "typeKey": "SectionType.Section",
        "label": "Teams",
        "single": true,
        "contentLimit": 1,
        "orderBy": null,
        "children": [
          {
            "customKey": "header",
            "typeKey": "DisplayType.TextBoxContent",
            "label": "Header above team members",
            "single": true,
            "contentLimit": 1,
            "orderBy": null
          },
          {
            "customKey": "teamMembers",
            "typeKey": "ReferenceType.ArticleBlurb",
            "label": "Team",
            "isList": true,
            "contentLimit": 4,
            "orderBy": 1
          }
        ]
      }
    ]
  }
}
```

Här är template-strukturen för nyhetsarkivet:
```json
{
  "view": {
    "viewKey": "Module",
    "typeKey": "ViewType.Module",
    "label": "Module",
    "moduleKey": "News",
    "moduleId": 4325,
    "customKey": "Module"
  },
  "templateTree": {
    "customKey": "Module",
    "typeKey": "ViewType.Module",
    "label": "Module",
    "isList": true,
    "orderBy": null,
    "children": [
      {
        "customKey": "heroPage",
        "typeKey": "SectionType.Section",
        "label": "Hero",
        "single": true,
        "contentLimit": 1,
        "orderBy": null,
        "children": [
          {
            "customKey": "header",
            "typeKey": "DisplayType.TextBoxContent",
            "label": "header",
            "single": true,
            "contentLimit": 1,
            "orderBy": null
          },
          {
            "customKey": "description",
            "typeKey": "DisplayType.TextAreaContent",
            "label": "Description",
            "single": true,
            "contentLimit": 1,
            "orderBy": 1
          },
          {
            "customKey": "media",
            "typeKey": "ReferenceType.Media",
            "label": "Media",
            "single": true,
            "contentLimit": 1,
            "orderBy": 2
          }
        ]
      },
      {
        "customKey": "newsList",
        "typeKey": "FeatureType.ArticleViewList",
        "label": "Newslist",
        "description": "",
        "noInput": true,
        "contentLimit": null,
        "orderBy": 1
      }
    ]
  }
}
```

Här är template-strukturen för enkel nyhet:
```json
{
  "view": {
    "viewKey": "News",
    "typeKey": "ViewType.Page",
    "label": "News",
    "moduleKey": "News",
    "moduleId": 4325
  },
  "templateTree": {
    "typeKey": "ViewType.Page",
    "label": "News",
    "isList": true,
    "orderBy": null,
    "children": [
      {
        "customKey": "image",
        "typeKey": "ReferenceType.DefaultImage",
        "label": "Image",
        "single": true,
        "contentLimit": 1,
        "orderBy": null
      },
      {
        "typeKey": "SectionType.Section",
        "label": "Article content",
        "single": true,
        "contentLimit": 1,
        "orderBy": 1,
        "children": [
          {
            "customKey": "preamble",
            "typeKey": "DisplayType.TextAreaContent",
            "label": "Preamble",
            "single": true,
            "contentLimit": 1,
            "orderBy": null
          },
          {
            "customKey": "html",
            "typeKey": "DisplayType.HtmlEditContent",
            "label": "Free text",
            "single": true,
            "contentLimit": 1,
            "orderBy": 1
          }
        ]
      },
      {
        "customKey": "newsList",
        "typeKey": "FeatureType.ArticleViewList",
        "label": "Newslist",
        "description": "",
        "noInput": true,
        "contentLimit": null,
        "orderBy": 2
      },
      {
        "customKey": "date",
        "typeKey": "DisplayType.Date",
        "label": "Datum",
        "single": true,
        "contentLimit": 1,
        "orderBy": 3
      },
      {
        "typeKey": "DisplayType.StructuredDataContainer",
        "label": "Strukturerad data",
        "single": true,
        "contentLimit": 1,
        "orderBy": 4
      }
    ]
  }
}
```

Här är template-strukturen för en alternativ vy "Company-timeline" under sidor:
```json
{
  "view": {
    "viewKey": "company-timeline",
    "typeKey": "ViewType.Page",
    "label": "Company timeline",
    "moduleKey": "WebPages",
    "moduleId": 40,
    "customKey": "Company-timeline"
  },
  "templateTree": {
    "customKey": "Company-timeline",
    "typeKey": "ViewType.Page",
    "label": "Company timeline",
    "isList": true,
    "orderBy": null,
    "children": [
      {
        "customKey": "heroPage",
        "typeKey": "SectionType.Section",
        "label": "Hero",
        "single": true,
        "contentLimit": 1,
        "orderBy": null,
        "children": [
          {
            "customKey": "header",
            "typeKey": "DisplayType.TextBoxContent",
            "label": "header",
            "single": true,
            "contentLimit": 1,
            "orderBy": null
          },
          {
            "customKey": "description",
            "typeKey": "DisplayType.TextAreaContent",
            "label": "Description",
            "single": true,
            "contentLimit": 1,
            "orderBy": 1
          },
          {
            "customKey": "media",
            "typeKey": "ReferenceType.Media",
            "label": "Media",
            "single": true,
            "contentLimit": 1,
            "orderBy": 2
          }
        ]
      },
      {
        "customKey": "timeline",
        "typeKey": "SectionType.Section",
        "label": "Tidslinje",
        "isList": true,
        "orderBy": 1,
        "children": [
          {
            "customKey": "year",
            "typeKey": "DisplayType.TextBoxContent",
            "label": "År",
            "single": true,
            "contentLimit": 1,
            "orderBy": null
          },
          {
            "customKey": "event",
            "typeKey": "DisplayType.TextBoxContent",
            "label": "Händelse",
            "single": true,
            "contentLimit": 1,
            "orderBy": 1
          },
          {
            "customKey": "description",
            "typeKey": "DisplayType.HtmlEditContent",
            "label": "Beskrivning",
            "single": true,
            "contentLimit": 1,
            "orderBy": 2
          },
          {
            "typeKey": "ReferenceType.Image",
            "label": "Bild",
            "single": true,
            "contentLimit": 1,
            "orderBy": 3
          }
        ]
      },
      {
        "typeKey": "SectionType.Flex",
        "label": "Flexsections",
        "isList": true,
        "orderBy": null,
        "children": [
          {
            "customKey": "introduction",
            "typeKey": "SectionType.Section",
            "label": "Introduction",
            "single": true,
            "contentLimit": 1,
            "orderBy": null,
            "children": [
              {
                "customKey": "tag",
                "typeKey": "DisplayType.TextBoxContent",
                "label": "Tag",
                "single": true,
                "contentLimit": 1,
                "orderBy": null
              },
              {
                "customKey": "header",
                "typeKey": "DisplayType.TextBoxContent",
                "label": "Header",
                "single": true,
                "contentLimit": 1,
                "orderBy": 1
              },
              {
                "customKey": "description",
                "typeKey": "DisplayType.TextAreaContent",
                "label": "Description",
                "single": true,
                "contentLimit": 1,
                "orderBy": 2
              },
              {
                "customKey": "link",
                "typeKey": "ReferenceType.Link",
                "label": "Link",
                "single": true,
                "contentLimit": 1,
                "orderBy": 3
              }
            ]
          },
          {
            "customKey": "facts",
            "typeKey": "SectionType.Section",
            "label": "Facts",
            "single": true,
            "contentLimit": 1,
            "orderBy": null,
            "children": [
              {
                "customKey": "header",
                "typeKey": "DisplayType.TextBoxContent",
                "label": "Header",
                "single": true,
                "contentLimit": 1,
                "orderBy": null
              },
              {
                "customKey": "boxes",
                "typeKey": "SectionType.Section",
                "label": "Box",
                "isList": true,
                "orderBy": 1,
                "children": [
                  {
                    "customKey": "number",
                    "typeKey": "DisplayType.TextBoxContent",
                    "label": "Number",
                    "single": true,
                    "contentLimit": 1,
                    "orderBy": null
                  },
                  {
                    "customKey": "text",
                    "typeKey": "DisplayType.TextBoxContent",
                    "label": "Text",
                    "single": true,
                    "contentLimit": 1,
                    "orderBy": 1
                  }
                ]
              }
            ]
          },
          {
            "customKey": "textMedia",
            "typeKey": "SectionType.Section",
            "label": "Text & Media",
            "isList": true,
            "orderBy": null,
            "children": [
              {
                "customKey": "reversed",
                "typeKey": "FeatureType.SelectOption",
                "label": "Reveresed",
                "description": "",
                "single": true,
                "contentLimit": 1,
                "orderBy": null
              },
              {
                "customKey": "image",
                "typeKey": "ReferenceType.Image",
                "label": "Image",
                "single": true,
                "contentLimit": 1,
                "orderBy": 1
              },
              {
                "customKey": "tag",
                "typeKey": "DisplayType.TextBoxContent",
                "label": "Tag",
                "single": true,
                "contentLimit": 1,
                "orderBy": 2
              },
              {
                "customKey": "header",
                "typeKey": "DisplayType.TextBoxContent",
                "label": "Header",
                "single": true,
                "contentLimit": 1,
                "orderBy": 3
              },
              {
                "customKey": "description",
                "typeKey": "DisplayType.TextAreaContent",
                "label": "Description",
                "single": true,
                "contentLimit": 1,
                "orderBy": 4
              }
            ]
          },
          {
            "customKey": "banner",
            "typeKey": "SectionType.Section",
            "label": "Banner",
            "single": true,
            "contentLimit": 1,
            "orderBy": null,
            "children": [
              {
                "customKey": "media",
                "typeKey": "ReferenceType.Media",
                "label": "Media",
                "single": true,
                "contentLimit": 1,
                "orderBy": null
              },
              {
                "customKey": "tag",
                "typeKey": "DisplayType.TextBoxContent",
                "label": "Tag",
                "single": true,
                "contentLimit": 1,
                "orderBy": 1
              },
              {
                "customKey": "header",
                "typeKey": "DisplayType.TextBoxContent",
                "label": "Header",
                "single": true,
                "contentLimit": 1,
                "orderBy": 2
              }
            ]
          },
          {
            "customKey": "textSection",
            "typeKey": "SectionType.Section",
            "label": "Textcontent",
            "single": true,
            "contentLimit": 1,
            "orderBy": null,
            "children": [
              {
                "customKey": "header",
                "typeKey": "DisplayType.TextBoxContent",
                "label": "Header",
                "single": true,
                "contentLimit": 1,
                "orderBy": null
              },
              {
                "customKey": "html",
                "typeKey": "DisplayType.HtmlEditContent",
                "label": "Texteditor",
                "single": true,
                "contentLimit": 1,
                "orderBy": 1
              }
            ]
          },
          {
            "customKey": "teams",
            "typeKey": "SectionType.Section",
            "label": "Teams",
            "single": true,
            "contentLimit": 1,
            "orderBy": null,
            "children": [
              {
                "customKey": "header",
                "typeKey": "DisplayType.TextBoxContent",
                "label": "Header",
                "single": true,
                "contentLimit": 1,
                "orderBy": null
              },
              {
                "customKey": "description",
                "typeKey": "DisplayType.TextAreaContent",
                "label": "Description",
                "single": true,
                "contentLimit": 1,
                "orderBy": 1
              },
              {
                "customKey": "teamMembers",
                "typeKey": "FeatureType.ArticleViewList",
                "label": "Team members",
                "description": "",
                "noInput": true,
                "contentLimit": null,
                "orderBy": 2
              }
            ]
          },
          {
            "customKey": "teams",
            "typeKey": "SectionType.Section",
            "label": "Teams",
            "single": true,
            "contentLimit": 1,
            "orderBy": null,
            "children": [
              {
                "customKey": "header",
                "typeKey": "DisplayType.TextBoxContent",
                "label": "Header above team members",
                "single": true,
                "contentLimit": 1,
                "orderBy": null
              },
              {
                "customKey": "teamMembers",
                "typeKey": "ReferenceType.ArticleBlurb",
                "label": "Team",
                "isList": true,
                "contentLimit": 4,
                "orderBy": 1
              }
            ]
          }
        ]
      }
    ]
  }
}
```