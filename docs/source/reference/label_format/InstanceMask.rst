**************
 InstanceMask
**************

InstanceMask is a type of label which is usually used for instance segmentation task.

In TensorBay, the structure of InstanceMask label is unified as follows::

    {
        "localPath": <str>
        "info": [
            {
                "instanceId": <int>
                "attributes": {
                    <key>: <value>
                    ...
                    ...
                }
            },
            ...
            ...
        ]
    }

``local_path`` is the storage path of the mask image. TensorBay only supports single-channel, gray-scale png images.
If the number of categories exceeds 256, the color depth of this image should be 16 bits, otherwise it is 8 bits.

There are pixels in the InstanceMask that do not represent the instance, such as backgrounds or borders. This information is written to the
``categories`` within the :class:`~tensorbay.label.label_mask.InstanceMaskSubcatalog`.

Each data can only be assigned with one :class:`~tensorbay.label.label_mask.InstanceMask` label.

To create a :class:`~tensorbay.label.label_mask.InstanceMask` label:

    >>> from tensorbay.label import InstanceMask
    >>> instance_mask_label = InstanceMask(local_path="/instance_mask/mask_image.png")
    >>> instance_mask_label
    InstanceMask("/instance_mask/mask_image.png")()

InstanceMask.all_attributes
===========================

`all_attributes` is a dictionary that stores attributes for each instance. Each attribute is stored in key-value pairs.
See :ref:`reference/label_format/CommonLabelProperties:attributes` for details.

To create `all_attributes`:

    >>> instance_mask_label.all_attributes = {1: {"occluded": True}, 2: {"occluded": True}}
    >>> instance_mask_label
    InstanceMask("/instance_mask/mask_image.png")(
      (all_attributes): {
        1: {
          'occluded': True
        },
        2: {
          'occluded': True
        }
      }
    )

.. note::

   In :class:`~tensorbay.label.label_mask.InstanceMask`, the key of `all_attributes` is instance id which should be an integer.

InstanceMaskSubcatalog
======================

Before adding the InstanceMask labels to data,
:class:`~tensorbay.label.label_mask.InstanceMaskSubcatalog` should be defined.

:class:`~tensorbay.label.label_mask.InstanceMaskSubcatalog` has mask categories and attributes,
see :ref:`reference/label_format/CommonSubcatalogProperties:mask category information` and
:ref:`reference/label_format/CommonSubcatalogProperties:attributes information` for details.

The catalog with only InstanceMask subcatalog is typically stored in a json file as follows::

    {
        "INSTANCE_MASK": {                                <object>*
            "description":                                <string>! -- Subcatalog description, (default: "").
            "isTracking":                                <boolean>! -- Whether this type of label in the dataset contains tracking
                                                                       information, (default: false).
            "categoryDelimiter":                          <string>  -- The delimiter in category names indicating subcategories.
                                                                       Recommended delimiter is ".". There is no "categoryDelimiter"
                                                                       field by default which means the category is of one level.
            "categories": [                                <array>  -- The categories of pixels in the InstanceMask that do not
                                                                       represent the instance, such as backgrounds or borders.
                {
                    "name":                               <string>* -- Category name.
                    "categoryId":                        <integer>* -- Category id.
                    "description":                        <string>! -- Category description, (default: "").
                },
                ...
                ...
            ],
            "attributes": [                                <array>  -- Attribute list, which contains all attribute information.
                {
                    "name":                               <string>* -- Attribute name.
                    "enum": [...],                         <array>  -- All possible options for the attribute.
                    "type":                      <string or array>  -- Type of the attribute including "boolean", "integer",
                                                                       "number", "string", "array" and "null". And it is not
                                                                       required when "enum" is provided.
                    "minimum":                            <number>  -- Minimum value of the attribute when type is "number".
                    "maximum":                            <number>  -- Maximum value of the attribute when type is "number".
                    "items": {                            <object>  -- Used only if the attribute type is "array".
                        "enum": [...],                     <array>  -- All possible options for elements in the attribute array.
                        "type":                  <string or array>  -- Type of elements in the attribute array.
                        "minimum":                        <number>  -- Minimum value of elements in the attribute array when type is
                                                                       "number".
                        "maximum":                        <number>  -- Maximum value of elements in the attribute array when type is
                                                                       "number".
                    },
                    "parentCategories": [...],             <array>  -- Indicates the category to which the attribute belongs. Do not
                                                                       add this field if it is a global attribute.
                    "description":                        <string>! -- Attribute description, (default: "").
                },
                ...
                ...
            ]
        }
    }

.. note::

   ``*`` indicates that the field is required. ``!`` indicates that the field has a default value.

To add a :class:`~tensorbay.label.label_mask.InstanceMask` label to one data:

    >>> from tensorbay.dataset import Data
    >>> data = Data("local_path")
    >>> data.label.instance_mask = instance_mask_label

.. note::

   One data can only have one InstanceMask label,
   See :attr:`Data.label.instance_mask<tensorbay.dataset.data.Data.label.instance_mask>` for details.
