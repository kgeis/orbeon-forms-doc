# TIFF Production



## Introduction

PDF files, whether produced automatically or via a [PDF template](../../form-builder/pdf-templates.md), can
be converted to TIFF image files for download or email.

## Availability

[SINCE Orbeon Forms 2016.1]

This is an Orbeon Forms PE feature.

## Setup

### Using buttons

TIFF files can be accessed directly from the Form Runner Summary Page and Detail Page using the `tiff` button.

Example for the Detail Page:

```xml
<property as="xs:string" name="oxf.fr.detail.buttons.acme.order">
    summary wizard-prev wizard-next pdf tiff save review
</property>
```

Example for the Summary Page:

```xml
<property as="xs:string"  name="oxf.fr.summary.buttons.acme.order">
    home review pdf tiff delete duplicate new
</property>
```

See also [Predefined buttons](../advanced/buttons-and-processes/README.md#predefined buttons).

### Properties

#### Compression type

The TIFF compression type can be selected with the `oxf.fr.detail.tiff.compression.type` property:

```xml
<property
    as="xs:string"
    name="oxf.fr.detail.tiff.compression.type.*.*"
    value="LZW"/>
```

- possible value: `CCITT RLE`, `CCITT T.4`, `CCITT T.6`, `LZW`, `JPEG`, `ZLib`, `PackBits`, `Deflate`, `EXIF JPEG` or `none`
- default: `LZW`

#### Compression quality

The compression quality can be selected with the `oxf.fr.detail.tiff.compression.quality` property:

```xml
<property
    as="xs:string"
    name="oxf.fr.detail.tiff.compression.quality.*.*"
    value="0.8"/>
```

- possible values: `0.0` to `1.0`
- default: `0.8`

#### Scale factor

The scale factor can be selected with the `oxf.fr.detail.tiff.scale` property:

```xml
<property
    as="xs:string"
    name="oxf.fr.detail.tiff.scale.*.*"
    value="3.0"/>
```

- possible values: positive double-precision number
- default: `3.0`

#### Custom TIFF filename

The following property dynamically controls the name of the TIFF file produced on the Detail Page. By default, if the property value is blank, the TIFF filename is a random id assigned to the current form session.

```xml
<property
    as="xs:string"
    name="oxf.fr.detail.tiff.filename.*.*"
    value=""/>
```

The value of the property, if not empty, is an XPath expression which runs in the context of the root element of the XML document containing form data. The trimmed string value of the result of the expression is used to determine the filename.

Example:

```xml
<property
    as="xs:string"
    name="oxf.fr.detail.tiff.filename.*.*"
    value="//customer-id"/>
```

If the form contains a `customer-id` field, the PDF filename will be the value of that field followed by `.tiff`. If the field is blank, the default, random id filename is used, as if the property had not been specified.

### Sending

You can *send* the URL of a TIFF file using the `content = "tiff-url"` parameter. See [Sending a TIFF URL](../advanced/buttons-and-processes/actions-form-runner.md#sending-a-tiff-url).

### Email

You can *email* the TIFF file using the following property:

```xml
<property
    as="xs:boolean"
    name="oxf.fr.email.attach-tiff.*.*"
    value="true"/>
```

See also [Email properties](../../configuration/properties/form-runner.md#email-settings).