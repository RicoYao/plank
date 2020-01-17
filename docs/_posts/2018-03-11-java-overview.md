---
layout: post
title: "Overview"
categories: java-reference
---

#### JSON Schema to Java type mapping

| Schema Property Type | Java Type |
| :--- | :--- |
| String | `String` |
| Boolean | `Boolean` |
| Integer | `Integer` |
| Number | `Double` |
| Date-time Property (String variant) | `java.util.Date` |
| URI Property (String variant) | `String` |
| JSON Pointer Property (`$ref`) | ModelType |
| Array Property | `List<Object>` |
| Array Property with Item types | `List<ModelType>` |
| Set Property (unique array) | `Set<Object>` |
| Set Property (unique array) with items | `Set<ModelType>` |
| Object Property | `Map<String, Object>` |
| Object Property with item types | `Map<String, ModelType>`  |
| Object Property with primitive item type | `Map<String, Integer>`, `Map<String, Float>`, `Map<String, Boolean>`, `Map<String, String>` |
| Algebraic Data Type (ADT)(oneOf)   | ADT Class (ModelType + Property name)|
| Nullable Property | `@Nullable T` |
| Integer Enum | `@IntDef` |
| String Enum | `@StringDef` |

#### Pin Schema 

```json
{
    "id": "pin.json",
    "title": "pin",
    "description" : "Schema definition of Pinterest Pin",
    "$schema": "http://json-schema.org/schema#",
    "type": "object",
    "properties": {
        "id" : { "type": "string" },
        "link" : {
            "type": "string",
            "format": "uri"
        },
        "url" : {
            "type": "string",
            "format": "uri"
        },
        "creator": {
            "type": "object",
            "additionalProperties": { "$ref": "user.json" }
        },
        "board": { "$ref": "board.json" },
        "created_at" : {
            "type": "string",
            "format": "date-time"
        },
        "note" : { "type": "string" },
        "color" : { "type": "string" },
        "counts": {
            "type": "object",
            "additionalProperties": { "type": "integer" }
        },
        "media": {
            "type": "object",
            "additionalProperties": { "type": "string" }
        },
        "attribution": {
            "type": "object",
            "additionalProperties": { "type": "string" }
        },
        "description" : { "type": "string" },
        "image": { "$ref": "image.json" },
        "tags": {
            "type": "array",
            "items": { "type": "object" }
        },
        "visual_search_attrs": {
            "type": "object"
        },
        "attribution_objects": {
            "type": "array",
            "items": {
                "oneOf": [{
                    "$ref": "board.json"
                }, {
                "$ref": "user.json"
                }]
            }
        },
        "in_stock" : {
            "type": "integer",
            "enum": [
                { "default" : -1, "description" : "unknown" },
                { "default" : 0, "description" : "out_of_stock" },
                { "default" : 1, "description" : "in_stock" }
            ]
        }
    },
    "required": ["id", "created_at", "creator"]
}
```

#### Java

```java
public class Pin {

    public enum PinInStock {
        @SerializedName("-1") UNKNOWN(-1), 
        @SerializedName("0") OUT_OF_STOCK(0), 
        @SerializedName("1") IN_STOCK(1);
        private final int value;
        PinInStock(int value) {
            this.value = value;
        }
        public int getValue() {
            return this.value;
        }
    }

    public static final String TYPE = "pin";

    @SerializedName("attribution") private @Nullable Map<String, String> attribution;
    @SerializedName("attribution_objects") private @Nullable List<PinAttributionObjects> attributionObjects;
    @SerializedName("board") private @Nullable Board board;
    @SerializedName("color") private @Nullable String color;
    @SerializedName("counts") private @Nullable Map<String, Integer> counts;
    @SerializedName("created_at") private @NonNull Date createdAt;
    @SerializedName("creator") private @NonNull Map<String, User> creator;
    @SerializedName("description") private @Nullable String description;
    @SerializedName("id") private @NonNull String uid;
    @SerializedName("image") private @Nullable Image image;
    @SerializedName("in_stock") private @Nullable PinInStock inStock;
    @SerializedName("link") private @Nullable String link;
    @SerializedName("media") private @Nullable Map<String, String> media;
    @SerializedName("note") private @Nullable String note;
    @SerializedName("tags") private @Nullable List<Map<String, Object>> tags;
    @SerializedName("url") private @Nullable String url;
    @SerializedName("visual_search_attrs") private @Nullable Map<String, Object> visualSearchAttrs;

    private static final int ATTRIBUTION_INDEX = 0;
    private static final int ATTRIBUTION_OBJECTS_INDEX = 1;
    private static final int BOARD_INDEX = 2;
    // ... omitted for brevity
    private static final int VISUAL_SEARCH_ATTRS_INDEX = 16;

    private boolean[] _bits;

    private Pin(
        @Nullable Map<String, String> attribution,
        @Nullable List<PinAttributionObjects> attributionObjects,
        @Nullable Board board,
        @Nullable String color,
        @Nullable Map<String, Integer> counts,
        @NonNull Date createdAt,
        @NonNull Map<String, User> creator,
        @Nullable String description,
        @NonNull String uid,
        @Nullable Image image,
        @Nullable PinInStock inStock,
        @Nullable String link,
        @Nullable Map<String, String> media,
        @Nullable String note,
        @Nullable List<Map<String, Object>> tags,
        @Nullable String url,
        @Nullable Map<String, Object> visualSearchAttrs,
        boolean[] _bits
    ) {
        this.attribution = attribution;
        this.attributionObjects = attributionObjects;
        this.board = board;
        this.color = color;
        this.counts = counts;
        this.createdAt = createdAt;
        this.creator = creator;
        this.description = description;
        this.uid = uid;
        this.image = image;
        this.inStock = inStock;
        this.link = link;
        this.media = media;
        this.note = note;
        this.tags = tags;
        this.url = url;
        this.visualSearchAttrs = visualSearchAttrs;
        this._bits = _bits;
    }

    @NonNull
    public static Pin.Builder builder() {
        return new Pin.Builder();
    }

    @NonNull
    public Pin.Builder toBuilder() {
        return new Pin.Builder(this);
    }

    @NonNull
    public Pin mergeFrom(@NonNull Pin model) {
        Pin.Builder builder = this.toBuilder();
        builder.mergeFrom(model);
        return builder.build();
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) {
            return true;
        }
        if (o == null || getClass() != o.getClass()) {
            return false;
        }
        Pin that = (Pin) o;
        return Objects.equals(this.inStock, that.inStock) &&
        Objects.equals(this.attribution, that.attribution) &&
        // ... omitted for brevity
        Objects.equals(this.visualSearchAttrs, that.visualSearchAttrs);
    }

    @Override
    public int hashCode() {
        return Objects.hash(attribution,
        attributionObjects,
        // ... omitted for brevity
        visualSearchAttrs);
    }

    /**
     * GETTERS
     */
    
    public @Nullable Map<String, String> getAttribution() {
        return this.attribution;
    }

    public @Nullable List<PinAttributionObjects> getAttributionObjects() {
        return this.attributionObjects;
    }

    // ...omitted for brevity

    public @Nullable String getUrl() {
        return this.url;
    }

    public @Nullable Map<String, Object> getVisualSearchAttrs() {
        return this.visualSearchAttrs;
    }

    /**
     * IsSet CHECKERS
     */

    public boolean getAttributionIsSet() {
        return this._bits.length > ATTRIBUTION_INDEX && this._bits[ATTRIBUTION_INDEX];
    }

    public boolean getAttributionObjectsIsSet() {
        return this._bits.length > ATTRIBUTION_OBJECTS_INDEX && this._bits[ATTRIBUTION_OBJECTS_INDEX];
    }

    // ... omitted for brevity

    /**
     * BUILDER
     */
    public static class Builder {

        private @Nullable Map<String, String> attribution;
        private @Nullable List<PinAttributionObjects> attributionObjects;
        private @Nullable Board board;
        private @Nullable String color;
        private @Nullable Map<String, Integer> counts;
        private @NonNull Date createdAt;
        private @NonNull Map<String, User> creator;
        private @Nullable String description;
        private @NonNull String uid;
        private @Nullable Image image;
        private @Nullable PinInStock inStock;
        private @Nullable String link;
        private @Nullable Map<String, String> media;
        private @Nullable String note;
        private @Nullable List<Map<String, Object>> tags;
        private @Nullable String url;
        private @Nullable Map<String, Object> visualSearchAttrs;

        private boolean[] _bits;

        private Builder() {
            this._bits = new boolean[17];
        }

        private Builder(@NonNull Pin model) {
            this.attribution = model.attribution;
            this.attributionObjects = model.attributionObjects;
            this.board = model.board;
            this.color = model.color;
            this.counts = model.counts;
            this.createdAt = model.createdAt;
            this.creator = model.creator;
            this.description = model.description;
            this.uid = model.uid;
            this.image = model.image;
            this.inStock = model.inStock;
            this.link = model.link;
            this.media = model.media;
            this.note = model.note;
            this.tags = model.tags;
            this.url = model.url;
            this.visualSearchAttrs = model.visualSearchAttrs;
            this._bits = model._bits;
        }

        @NonNull
        public Builder setAttribution(@Nullable Map<String, String> value) {
            this.attribution = value;
            if (this._bits.length > ATTRIBUTION_INDEX) {
                this._bits[ATTRIBUTION_INDEX] = true;
            }
            return this;
        }

        // ... the rest of the fields are omitted for brevity

        public @Nullable Map<String, String> getAttribution() {
            return this.attribution;
        }

        // ... the rest of the fields are omitted for brevity

        @NonNull
        public Pin build() {
            return new Pin(
            this.attribution,
            this.attributionObjects,
            this.board,
            this.color,
            this.counts,
            this.createdAt,
            this.creator,
            this.description,
            this.uid,
            this.image,
            this.inStock,
            this.link,
            this.media,
            this.note,
            this.tags,
            this.url,
            this.visualSearchAttrs,
            this._bits
            );
        }

        public void mergeFrom(@NonNull Pin model) {
            if (model._bits.length > ATTRIBUTION_INDEX && model._bits[ATTRIBUTION_INDEX]) {
                this.attribution = model.attribution;
                this._bits[ATTRIBUTION_INDEX] = true;
            }
            if (model._bits.length > ATTRIBUTION_OBJECTS_INDEX && model._bits[ATTRIBUTION_OBJECTS_INDEX]) {
                this.attributionObjects = model.attributionObjects;
                this._bits[ATTRIBUTION_OBJECTS_INDEX] = true;
            }
            
            // ... the rest of the fields are omitted for brevity
        }
    }

    /**
     * TypeAdapterFactory
     */
    public static class PinTypeAdapterFactory implements TypeAdapterFactory {

        @Nullable
        @Override
        public <T> TypeAdapter<T> create(@NonNull Gson gson, @NonNull TypeToken<T> typeToken) {
            if (!Pin.class.isAssignableFrom(typeToken.getRawType())) {
                return null;
            }
            return (TypeAdapter<T>) new PinTypeAdapter(gson);
        }
    }

    /**
     * TypeAdapterFactory
     */
    private static class PinTypeAdapter extends TypeAdapter<Pin> {
        // ... omitted for brevity
    }

    /**
     * Algabraeic Data Type
     */
    public static final class PinAttributionObjects {

        private @Nullable Board value0;
        private @Nullable User value1;

        private PinAttributionObjects() {
        }

        public PinAttributionObjects(@NonNull Board value) {
            this.value0 = value;
        }

        public PinAttributionObjects(@NonNull User value) {
            this.value1 = value;
        }

        @Nullable
        public <R> R matchPinAttributionObjects(PinAttributionObjectsMatcher<R> matcher) {
            if (value0 != null) {
                return matcher.match(value0);
            }
            if (value1 != null) {
                return matcher.match(value1);
            }
            return null;
        }

        public static class PinAttributionObjectsTypeAdapterFactory implements TypeAdapterFactory {

            @Nullable
            @Override
            public <T> TypeAdapter<T> create(@NonNull Gson gson, @NonNull TypeToken<T> typeToken) {
                if (!PinAttributionObjects.class.isAssignableFrom(typeToken.getRawType())) {
                    return null;
                }
                return (TypeAdapter<T>) new PinAttributionObjectsTypeAdapter(gson);
            }
        }

        private static class PinAttributionObjectsTypeAdapter extends TypeAdapter<PinAttributionObjects> {

            private final Gson gson;
            private TypeAdapter<Board> boardTypeAdapter;
            private TypeAdapter<User> userTypeAdapter;

            public PinAttributionObjectsTypeAdapter(Gson gson) {
                this.gson = gson;
            }

            @Override
            public void write(@NonNull JsonWriter writer, PinAttributionObjects value) throws IOException {
                writer.nullValue();
            }

            @Nullable
            @Override
            public PinAttributionObjects read(@NonNull JsonReader reader) throws IOException {
                if (reader.peek() == JsonToken.NULL) {
                    reader.nextNull();
                    return null;
                }
                if (reader.peek() == JsonToken.BEGIN_OBJECT) {
                    JsonObject jsonObject = this.gson.fromJson(reader, JsonObject.class);
                    String type;
                    try {
                        type = jsonObject.get("type").getAsString();
                    } catch (Exception e) {
                        return new PinAttributionObjects();
                    }
                    if (type == null) {
                        return new PinAttributionObjects();
                    }
                    switch (type) {
                        case ("board"):
                            if (this.boardTypeAdapter == null) {
                                this.boardTypeAdapter = this.gson.getAdapter(Board.class).nullSafe();
                            }
                            return new PinAttributionObjects(boardTypeAdapter.fromJsonTree(jsonObject));
                        case ("user"):
                            if (this.userTypeAdapter == null) {
                                this.userTypeAdapter = this.gson.getAdapter(User.class).nullSafe();
                            }
                            return new PinAttributionObjects(userTypeAdapter.fromJsonTree(jsonObject));
                        default:
                            return new PinAttributionObjects();
                    }
                }
                reader.skipValue();
                return new PinAttributionObjects();
            }
        }

        public interface PinAttributionObjectsMatcher<R> {
            R match(@NonNull Board value0);
            R match(@NonNull User value1);
        }
    }
}
```


## Dependencies

The Java output currently has external dependencies. 

### Gson
To support JSON deserialization, we will be utilizing the `@SerializedName` annotation in [Gson](https://github.com/google/gson) to facilitate the conversion between Plank types and JSON objects.

## Implementation Details

### Nullability

For properties that are not required (i.e. nullable), we will annotate them with [`@Nullable`](https://developer.android.com/reference/android/support/annotation/Nullable.html).

Properties that are required, we will annotate them with [`@NonNull`](https://developer.android.com/reference/android/support/annotation/NonNull.html). 

### TypeAdapters

A Gson TypeAdapterFactory is generated for the model as a sublcass. If you plan to deserialize Json to a model (or vice versa), you should register this TypeAdapterFactory in order in order to guarantee proper behavior for merging and field "is set" checking.
`gsonBuilder.registerTypeAdapterFactory(new Pin.PinTypeAdapterFactory());`

### Enums

Enums are classes in Java. Using Enums over [`@IntDef`](https://developer.android.com/reference/android/support/annotation/IntDef.html) adds about 13x more memory as if you would have used [`@IntDef`](https://developer.android.com/reference/android/support/annotation/IntDef.html).

For similar reasons, we will be utilizing [@StringDef](https://developer.android.com/reference/android/support/annotation/StringDef.html
) for String enumerations.

For more in-depth information, watch this [video](https://www.youtube.com/watch?v=Hzs6OBcvNQE) by Android Developers.


