---
title: Linking to External Services
layout: sub-page
categories: [annex, service, services, spec-doc, specifications]
---

## Status of this Document
{:.no_toc}

This document is not subject to semantic versioning.

_Copyright © 2012-2014 Editors and contributors. Published by the IIIF under the CC-BY license._

**Editors**

  * Benjamin Albritton, _Stanford University_
  * Michael Appleby, _Yale University_
  * Robert Sanderson, _Stanford University_
  * Jon Stroop, _Princeton University_
  * Simeon Warner, _Cornell University_
  {: .names}

## Abstract
{:.no_toc}
This document describes the set of related services that have been identified within the IIIF as useful to reference from within the IIIF APIs.  This primarily relates to the [Presentation API][prezi-api] but may be of use beyond that.

Please send feedback to [iiif-discuss@googlegroups.com][iiif-discuss]

## Table of Contents
{:.no_toc}

* Table of Discontent (will be replaced by macro)
{:toc}

## 1. Introduction

There are many desirable features that could be usefully included in resource descriptions beyond those already defined in the [Presentation API][prezi-api].  However, in order to keep the API manageable and lean enough to be understood, implemented and validated, any feature which is not able to be justified as universally applicable will be imported as a service from an external resource.  The adoption of [JSON-LD][json-ld] is paramount in this respect, as it gives a solid basis for interoperability and disambiguation between systems.

## 2. Requirements

Services _SHOULD_ be valid [JSON-LD][json-ld], and if so _MUST_ have their own `@context` supplied in the Presentation API response.  Services _SHOULD_ have an `@id` that can be dereferenced, and if so, the representation retrieved _SHOULD_ be JSON-LD.

Services _MAY_ be included either by reference or embedded within the Presentation API documents if appropriate.  The decision as to whether to embed or reference is left up to the implementer, however embedded descriptions should be kept as short as possible.  If the only properties of the object are `@context` and `@id`, then the client _SHOULD_ retrieve the resource from the URI given in `@id`.

Services _SHOULD_ have a `profile` URI which can be used to determine the type of service.  The representation of the URI _SHOULD_ be a human or machine readable description of the service.

{% highlight json %}
{
  "service": {
    "@context": "http://example.org/ns/jsonld/context.json",
    "@id": "http://example.org/service/example.json",
    "profile": "http://example.org/docs/example-service.html"
    // Additional keys may be embedded here, if not, the @id should be retrieved 
  }
}
{% endhighlight %}

## 3. Recognized Services

### 3.1 Image Information

The main use of services is to provide a reference from the [Presentation API][prezi-api] to the content to be displayed via the [Image API][image-api].  The JSON-LD content to be referenced or embedded is the Image Information document, also known as `info.json`.  The service _MUST_ have the `@context`, `@id` and `profile` keys, pointing to the context document, service base URI and compliance level profile respectively.

{% highlight json %}
{
  "service": {
    "@context" : "http://iiif.io/api/image/{{ site.image_api.latest.major }}/context.json",
    "@id" : "http://www.example.org/image-service/abcd1234",
    "profile": "http://iiif.io/api/image/1/level2.json"
  } 
}
{% endhighlight %}

The service _MAY_ have additional information embedded from the Image Information document to prevent the need to retrieve and parse it separately.  In this case, the profile _MAY_ also point to the profile of what functionality is supported, as described in the Image API.

{% highlight json %}
{
  "service": {
    "@context" : "http://iiif.io/api/image/{{ site.image_api.latest.major }}/context.json",
    "@id" : "http://www.example.org/image-service/abcd1234",
    "profile": "http://iiif.io/api/image/1/level2.json",
    "protocol": "http://iiif.io/api/image",
    "width" : 6000,
    "height" : 4000,
    "scale_factors" : [ 1, 2, 4 ],
    "sizes" : [ "150,100", "360,240", "3600,2400" ],
    "tile_width" : 1024,
    "tile_height" : 1024
  } 
}
{% endhighlight %}

### 3.2 GeoJSON

### 3.3 Physical Dimensions

For digitized objects, it is often useful to know the physical dimensions of the object.  If available, it would allow a client to present a ruler, or other rendition of physical scale, to the user.  
This information might be available, but frequently:
  * It is not available at all
  * It is unreliable when it is recorded
  * It is different for every view of an object
  * The Canvas dimensions don't accurately reflect only the physical object, but are derived from an image that includes a ruler, color bar, or the scanning bed.

As the Presentation API already includes an aspect ratio for the Canvas, the physical dimensions service need only report two additional pieces of information: the scale factor from canvas dimensions to physical dimensions, and the units for those generated physical dimensions.  It is _RECOMMENDED_ that the information always be embedded rather than requiring the client to retrieve it with a 

| Property        | Required? | Description |
| --------------- | --------- | ----------- |
| `@context`      | Required  | The string "http://iiif.io/api/annex/service/physdim/1.0/context.json" |
| `profile`       | Required  | The string "http://iiif.io/api/annex/service/physdim" |
| `physicalScale` | Required  | The floating point ratio to convert from the canvas height and width to the physical objects height and width.  |
| `physicalUnits` | Required  | The physical units for the generated height and width.  Possible values are: "mm", "cm", in" |
{: .image-api-table}

The following example demonstrates the resulting structure:

{% highlight json %}
{
  "service": {
    "@context": "http://iiif.io/api/annex/service/physdim/1.0/context.json",
    "profile": "http://iiif.io/api/annex/service/physdim",
    "physicalScale": 0.025,
    "physicalUnits": "mm"
  }
}
{% endhighlight %}


## Appendices

### A. Acknowledgements

### B. Changelog



   [iiif-discuss]: mailto:iiif-discuss@googlegroups.com "Email Discussion List"
   [image-api]: /api/image/{{ site.image_api.latest.major }}.{{ site.image_api.latest.minor }}/ "Image API"
   [prezi-api]: /api/presentation/{{ site.presentation_api.latest.major }}.{{ site.presentation_api.latest.minor }}/ "Presentation API"
   [json-ld]: http://www.w3.org/TR/json-ld/ "JSON-LD"