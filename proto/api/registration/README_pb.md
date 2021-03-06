# Protocol Documentation
<a name="top"/>

## Table of Contents

- [http.proto](#http.proto)
    - [CustomHttpPattern](#google.api.CustomHttpPattern)
    - [Http](#google.api.Http)
    - [HttpRule](#google.api.HttpRule)
  
  
  
  

- [descriptor.proto](#descriptor.proto)
    - [DescriptorProto](#google.protobuf.DescriptorProto)
    - [DescriptorProto.ExtensionRange](#google.protobuf.DescriptorProto.ExtensionRange)
    - [DescriptorProto.ReservedRange](#google.protobuf.DescriptorProto.ReservedRange)
    - [EnumDescriptorProto](#google.protobuf.EnumDescriptorProto)
    - [EnumOptions](#google.protobuf.EnumOptions)
    - [EnumValueDescriptorProto](#google.protobuf.EnumValueDescriptorProto)
    - [EnumValueOptions](#google.protobuf.EnumValueOptions)
    - [FieldDescriptorProto](#google.protobuf.FieldDescriptorProto)
    - [FieldOptions](#google.protobuf.FieldOptions)
    - [FileDescriptorProto](#google.protobuf.FileDescriptorProto)
    - [FileDescriptorSet](#google.protobuf.FileDescriptorSet)
    - [FileOptions](#google.protobuf.FileOptions)
    - [GeneratedCodeInfo](#google.protobuf.GeneratedCodeInfo)
    - [GeneratedCodeInfo.Annotation](#google.protobuf.GeneratedCodeInfo.Annotation)
    - [MessageOptions](#google.protobuf.MessageOptions)
    - [MethodDescriptorProto](#google.protobuf.MethodDescriptorProto)
    - [MethodOptions](#google.protobuf.MethodOptions)
    - [OneofDescriptorProto](#google.protobuf.OneofDescriptorProto)
    - [OneofOptions](#google.protobuf.OneofOptions)
    - [ServiceDescriptorProto](#google.protobuf.ServiceDescriptorProto)
    - [ServiceOptions](#google.protobuf.ServiceOptions)
    - [SourceCodeInfo](#google.protobuf.SourceCodeInfo)
    - [SourceCodeInfo.Location](#google.protobuf.SourceCodeInfo.Location)
    - [UninterpretedOption](#google.protobuf.UninterpretedOption)
    - [UninterpretedOption.NamePart](#google.protobuf.UninterpretedOption.NamePart)
  
    - [FieldDescriptorProto.Label](#google.protobuf.FieldDescriptorProto.Label)
    - [FieldDescriptorProto.Type](#google.protobuf.FieldDescriptorProto.Type)
    - [FieldOptions.CType](#google.protobuf.FieldOptions.CType)
    - [FieldOptions.JSType](#google.protobuf.FieldOptions.JSType)
    - [FileOptions.OptimizeMode](#google.protobuf.FileOptions.OptimizeMode)
    - [MethodOptions.IdempotencyLevel](#google.protobuf.MethodOptions.IdempotencyLevel)
  
  
  

- [annotations.proto](#annotations.proto)
  
  
    - [File-level Extensions](#annotations.proto-extensions)
  
  

- [common.proto](#common.proto)
    - [AttestationData](#spire.common.AttestationData)
    - [Bundle](#spire.common.Bundle)
    - [Certificate](#spire.common.Certificate)
    - [Empty](#spire.common.Empty)
    - [PublicKey](#spire.common.PublicKey)
    - [RegistrationEntries](#spire.common.RegistrationEntries)
    - [RegistrationEntry](#spire.common.RegistrationEntry)
    - [Selector](#spire.common.Selector)
    - [Selectors](#spire.common.Selectors)
  
  
  
  

- [registration.proto](#registration.proto)
    - [Bundle](#spire.api.registration.Bundle)
    - [DeleteFederatedBundleRequest](#spire.api.registration.DeleteFederatedBundleRequest)
    - [FederatedBundle](#spire.api.registration.FederatedBundle)
    - [FederatedBundleID](#spire.api.registration.FederatedBundleID)
    - [JoinToken](#spire.api.registration.JoinToken)
    - [ParentID](#spire.api.registration.ParentID)
    - [RegistrationEntryID](#spire.api.registration.RegistrationEntryID)
    - [SpiffeID](#spire.api.registration.SpiffeID)
    - [UpdateEntryRequest](#spire.api.registration.UpdateEntryRequest)
  
    - [DeleteFederatedBundleRequest.Mode](#spire.api.registration.DeleteFederatedBundleRequest.Mode)
  
  
    - [Registration](#spire.api.registration.Registration)
  

- [Scalar Value Types](#scalar-value-types)



<a name="http.proto"/>
<p align="right"><a href="#top">Top</a></p>

## http.proto



<a name="google.api.CustomHttpPattern"/>

### CustomHttpPattern
A custom pattern is used for defining custom HTTP verb.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| kind | [string](#string) |  | The name of this custom HTTP verb. |
| path | [string](#string) |  | The path matched by this custom verb. |






<a name="google.api.Http"/>

### Http
Defines the HTTP configuration for a service. It contains a list of
[HttpRule][google.api.HttpRule], each specifying the mapping of an RPC method
to one or more HTTP REST API methods.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| rules | [HttpRule](#google.api.HttpRule) | repeated | A list of HTTP configuration rules that apply to individual API methods. NOTE:** All service configuration rules follow &#34;last one wins&#34; order. |






<a name="google.api.HttpRule"/>

### HttpRule
`HttpRule` defines the mapping of an RPC method to one or more HTTP
REST APIs.  The mapping determines what portions of the request
message are populated from the path, query parameters, or body of
the HTTP request.  The mapping is typically specified as an
`google.api.http` annotation, see &#34;google/api/annotations.proto&#34;
for details.

The mapping consists of a field specifying the path template and
method kind.  The path template can refer to fields in the request
message, as in the example below which describes a REST GET
operation on a resource collection of messages:


service Messaging {
rpc GetMessage(GetMessageRequest) returns (Message) {
option (google.api.http).get = &#34;/v1/messages/{message_id}/{sub.subfield}&#34;;
}
}
message GetMessageRequest {
message SubMessage {
string subfield = 1;
}
string message_id = 1; // mapped to the URL
SubMessage sub = 2;    // `sub.subfield` is url-mapped
}
message Message {
string text = 1; // content of the resource
}

The same http annotation can alternatively be expressed inside the
`GRPC API Configuration` YAML file.

http:
rules:
- selector: &lt;proto_package_name&gt;.Messaging.GetMessage
get: /v1/messages/{message_id}/{sub.subfield}

This definition enables an automatic, bidrectional mapping of HTTP
JSON to RPC. Example:

HTTP | RPC
-----|-----
`GET /v1/messages/123456/foo`  | `GetMessage(message_id: &#34;123456&#34; sub: SubMessage(subfield: &#34;foo&#34;))`

In general, not only fields but also field paths can be referenced
from a path pattern. Fields mapped to the path pattern cannot be
repeated and must have a primitive (non-message) type.

Any fields in the request message which are not bound by the path
pattern automatically become (optional) HTTP query
parameters. Assume the following definition of the request message:


message GetMessageRequest {
message SubMessage {
string subfield = 1;
}
string message_id = 1; // mapped to the URL
int64 revision = 2;    // becomes a parameter
SubMessage sub = 3;    // `sub.subfield` becomes a parameter
}


This enables a HTTP JSON to RPC mapping as below:

HTTP | RPC
-----|-----
`GET /v1/messages/123456?revision=2&amp;sub.subfield=foo` | `GetMessage(message_id: &#34;123456&#34; revision: 2 sub: SubMessage(subfield: &#34;foo&#34;))`

Note that fields which are mapped to HTTP parameters must have a
primitive type or a repeated primitive type. Message types are not
allowed. In the case of a repeated type, the parameter can be
repeated in the URL, as in `...?param=A&amp;param=B`.

For HTTP method kinds which allow a request body, the `body` field
specifies the mapping. Consider a REST update method on the
message resource collection:


service Messaging {
rpc UpdateMessage(UpdateMessageRequest) returns (Message) {
option (google.api.http) = {
put: &#34;/v1/messages/{message_id}&#34;
body: &#34;message&#34;
};
}
}
message UpdateMessageRequest {
string message_id = 1; // mapped to the URL
Message message = 2;   // mapped to the body
}


The following HTTP JSON to RPC mapping is enabled, where the
representation of the JSON in the request body is determined by
protos JSON encoding:

HTTP | RPC
-----|-----
`PUT /v1/messages/123456 { &#34;text&#34;: &#34;Hi!&#34; }` | `UpdateMessage(message_id: &#34;123456&#34; message { text: &#34;Hi!&#34; })`

The special name `*` can be used in the body mapping to define that
every field not bound by the path template should be mapped to the
request body.  This enables the following alternative definition of
the update method:

service Messaging {
rpc UpdateMessage(Message) returns (Message) {
option (google.api.http) = {
put: &#34;/v1/messages/{message_id}&#34;
body: &#34;*&#34;
};
}
}
message Message {
string message_id = 1;
string text = 2;
}


The following HTTP JSON to RPC mapping is enabled:

HTTP | RPC
-----|-----
`PUT /v1/messages/123456 { &#34;text&#34;: &#34;Hi!&#34; }` | `UpdateMessage(message_id: &#34;123456&#34; text: &#34;Hi!&#34;)`

Note that when using `*` in the body mapping, it is not possible to
have HTTP parameters, as all fields not bound by the path end in
the body. This makes this option more rarely used in practice of
defining REST APIs. The common usage of `*` is in custom methods
which don&#39;t use the URL at all for transferring data.

It is possible to define multiple HTTP methods for one RPC by using
the `additional_bindings` option. Example:

service Messaging {
rpc GetMessage(GetMessageRequest) returns (Message) {
option (google.api.http) = {
get: &#34;/v1/messages/{message_id}&#34;
additional_bindings {
get: &#34;/v1/users/{user_id}/messages/{message_id}&#34;
}
};
}
}
message GetMessageRequest {
string message_id = 1;
string user_id = 2;
}


This enables the following two alternative HTTP JSON to RPC
mappings:

HTTP | RPC
-----|-----
`GET /v1/messages/123456` | `GetMessage(message_id: &#34;123456&#34;)`
`GET /v1/users/me/messages/123456` | `GetMessage(user_id: &#34;me&#34; message_id: &#34;123456&#34;)`

# Rules for HTTP mapping

The rules for mapping HTTP path, query parameters, and body fields
to the request message are as follows:

1. The `body` field specifies either `*` or a field path, or is
omitted. If omitted, it assumes there is no HTTP body.
2. Leaf fields (recursive expansion of nested messages in the
request) can be classified into three types:
(a) Matched in the URL template.
(b) Covered by body (if body is `*`, everything except (a) fields;
else everything under the body field)
(c) All other fields.
3. URL query parameters found in the HTTP request are mapped to (c) fields.
4. Any body sent with an HTTP request can contain only (b) fields.

The syntax of the path template is as follows:

Template = &#34;/&#34; Segments [ Verb ] ;
Segments = Segment { &#34;/&#34; Segment } ;
Segment  = &#34;*&#34; | &#34;**&#34; | LITERAL | Variable ;
Variable = &#34;{&#34; FieldPath [ &#34;=&#34; Segments ] &#34;}&#34; ;
FieldPath = IDENT { &#34;.&#34; IDENT } ;
Verb     = &#34;:&#34; LITERAL ;

The syntax `*` matches a single path segment. It follows the semantics of
[RFC 6570](https://tools.ietf.org/html/rfc6570) Section 3.2.2 Simple String
Expansion.

The syntax `**` matches zero or more path segments. It follows the semantics
of [RFC 6570](https://tools.ietf.org/html/rfc6570) Section 3.2.3 Reserved
Expansion. NOTE: it must be the last segment in the path except the Verb.

The syntax `LITERAL` matches literal text in the URL path.

The syntax `Variable` matches the entire path as specified by its template;
this nested template must not contain further variables. If a variable
matches a single path segment, its template may be omitted, e.g. `{var}`
is equivalent to `{var=*}`.

NOTE: the field paths in variables and in the `body` must not refer to
repeated fields or map fields.

Use CustomHttpPattern to specify any HTTP method that is not included in the
`pattern` field, such as HEAD, or &#34;*&#34; to leave the HTTP method unspecified for
a given URL path rule. The wild-card rule is useful for services that provide
content to Web (HTML) clients.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| selector | [string](#string) |  | Selects methods to which this rule applies. Refer to [selector][google.api.DocumentationRule.selector] for syntax details. |
| get | [string](#string) |  | Used for listing and getting information about resources. |
| put | [string](#string) |  | Used for updating a resource. |
| post | [string](#string) |  | Used for creating a resource. |
| delete | [string](#string) |  | Used for deleting a resource. |
| patch | [string](#string) |  | Used for updating a resource. |
| custom | [CustomHttpPattern](#google.api.CustomHttpPattern) |  | Custom pattern is used for defining custom verbs. |
| body | [string](#string) |  | The name of the request field whose value is mapped to the HTTP body, or `*` for mapping all fields not captured by the path pattern to the HTTP body. NOTE: the referred field must not be a repeated field and must be present at the top-level of request message type. |
| additional_bindings | [HttpRule](#google.api.HttpRule) | repeated | Additional HTTP bindings for the selector. Nested bindings must not contain an `additional_bindings` field themselves (that is, the nesting may only be one level deep). |





 

 

 

 



<a name="descriptor.proto"/>
<p align="right"><a href="#top">Top</a></p>

## descriptor.proto



<a name="google.protobuf.DescriptorProto"/>

### DescriptorProto
Describes a message type.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| name | [string](#string) | optional |  |
| field | [FieldDescriptorProto](#google.protobuf.FieldDescriptorProto) | repeated |  |
| extension | [FieldDescriptorProto](#google.protobuf.FieldDescriptorProto) | repeated |  |
| nested_type | [DescriptorProto](#google.protobuf.DescriptorProto) | repeated |  |
| enum_type | [EnumDescriptorProto](#google.protobuf.EnumDescriptorProto) | repeated |  |
| extension_range | [DescriptorProto.ExtensionRange](#google.protobuf.DescriptorProto.ExtensionRange) | repeated |  |
| oneof_decl | [OneofDescriptorProto](#google.protobuf.OneofDescriptorProto) | repeated |  |
| options | [MessageOptions](#google.protobuf.MessageOptions) | optional |  |
| reserved_range | [DescriptorProto.ReservedRange](#google.protobuf.DescriptorProto.ReservedRange) | repeated |  |
| reserved_name | [string](#string) | repeated | Reserved field names, which may not be used by fields in the same message. A given name may only be reserved once. |






<a name="google.protobuf.DescriptorProto.ExtensionRange"/>

### DescriptorProto.ExtensionRange



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| start | [int32](#int32) | optional |  |
| end | [int32](#int32) | optional |  |






<a name="google.protobuf.DescriptorProto.ReservedRange"/>

### DescriptorProto.ReservedRange
Range of reserved tag numbers. Reserved tag numbers may not be used by
fields or extension ranges in the same message. Reserved ranges may
not overlap.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| start | [int32](#int32) | optional | Inclusive. |
| end | [int32](#int32) | optional | Exclusive. |






<a name="google.protobuf.EnumDescriptorProto"/>

### EnumDescriptorProto
Describes an enum type.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| name | [string](#string) | optional |  |
| value | [EnumValueDescriptorProto](#google.protobuf.EnumValueDescriptorProto) | repeated |  |
| options | [EnumOptions](#google.protobuf.EnumOptions) | optional |  |






<a name="google.protobuf.EnumOptions"/>

### EnumOptions



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| allow_alias | [bool](#bool) | optional | Set this option to true to allow mapping different tag names to the same value. |
| deprecated | [bool](#bool) | optional | Is this enum deprecated? Depending on the target platform, this can emit Deprecated annotations for the enum, or it will be completely ignored; in the very least, this is a formalization for deprecating enums. |
| uninterpreted_option | [UninterpretedOption](#google.protobuf.UninterpretedOption) | repeated | The parser stores options it doesn&#39;t recognize here. See above. |






<a name="google.protobuf.EnumValueDescriptorProto"/>

### EnumValueDescriptorProto
Describes a value within an enum.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| name | [string](#string) | optional |  |
| number | [int32](#int32) | optional |  |
| options | [EnumValueOptions](#google.protobuf.EnumValueOptions) | optional |  |






<a name="google.protobuf.EnumValueOptions"/>

### EnumValueOptions



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| deprecated | [bool](#bool) | optional | Is this enum value deprecated? Depending on the target platform, this can emit Deprecated annotations for the enum value, or it will be completely ignored; in the very least, this is a formalization for deprecating enum values. |
| uninterpreted_option | [UninterpretedOption](#google.protobuf.UninterpretedOption) | repeated | The parser stores options it doesn&#39;t recognize here. See above. |






<a name="google.protobuf.FieldDescriptorProto"/>

### FieldDescriptorProto
Describes a field within a message.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| name | [string](#string) | optional |  |
| number | [int32](#int32) | optional |  |
| label | [FieldDescriptorProto.Label](#google.protobuf.FieldDescriptorProto.Label) | optional |  |
| type | [FieldDescriptorProto.Type](#google.protobuf.FieldDescriptorProto.Type) | optional | If type_name is set, this need not be set. If both this and type_name are set, this must be one of TYPE_ENUM, TYPE_MESSAGE or TYPE_GROUP. |
| type_name | [string](#string) | optional | For message and enum types, this is the name of the type. If the name starts with a &#39;.&#39;, it is fully-qualified. Otherwise, C&#43;&#43;-like scoping rules are used to find the type (i.e. first the nested types within this message are searched, then within the parent, on up to the root namespace). |
| extendee | [string](#string) | optional | For extensions, this is the name of the type being extended. It is resolved in the same manner as type_name. |
| default_value | [string](#string) | optional | For numeric types, contains the original text representation of the value. For booleans, &#34;true&#34; or &#34;false&#34;. For strings, contains the default text contents (not escaped in any way). For bytes, contains the C escaped value. All bytes &gt;= 128 are escaped. TODO(kenton): Base-64 encode? |
| oneof_index | [int32](#int32) | optional | If set, gives the index of a oneof in the containing type&#39;s oneof_decl list. This field is a member of that oneof. |
| json_name | [string](#string) | optional | JSON name of this field. The value is set by protocol compiler. If the user has set a &#34;json_name&#34; option on this field, that option&#39;s value will be used. Otherwise, it&#39;s deduced from the field&#39;s name by converting it to camelCase. |
| options | [FieldOptions](#google.protobuf.FieldOptions) | optional |  |






<a name="google.protobuf.FieldOptions"/>

### FieldOptions



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| ctype | [FieldOptions.CType](#google.protobuf.FieldOptions.CType) | optional | The ctype option instructs the C&#43;&#43; code generator to use a different representation of the field than it normally would. See the specific options below. This option is not yet implemented in the open source release -- sorry, we&#39;ll try to include it in a future version! |
| packed | [bool](#bool) | optional | The packed option can be enabled for repeated primitive fields to enable a more efficient representation on the wire. Rather than repeatedly writing the tag and type for each element, the entire array is encoded as a single length-delimited blob. In proto3, only explicit setting it to false will avoid using packed encoding. |
| jstype | [FieldOptions.JSType](#google.protobuf.FieldOptions.JSType) | optional | The jstype option determines the JavaScript type used for values of the field. The option is permitted only for 64 bit integral and fixed types (int64, uint64, sint64, fixed64, sfixed64). By default these types are represented as JavaScript strings. This avoids loss of precision that can happen when a large value is converted to a floating point JavaScript numbers. Specifying JS_NUMBER for the jstype causes the generated JavaScript code to use the JavaScript &#34;number&#34; type instead of strings. This option is an enum to permit additional types to be added, e.g. goog.math.Integer. |
| lazy | [bool](#bool) | optional | Should this field be parsed lazily? Lazy applies only to message-type fields. It means that when the outer message is initially parsed, the inner message&#39;s contents will not be parsed but instead stored in encoded form. The inner message will actually be parsed when it is first accessed. This is only a hint. Implementations are free to choose whether to use eager or lazy parsing regardless of the value of this option. However, setting this option true suggests that the protocol author believes that using lazy parsing on this field is worth the additional bookkeeping overhead typically needed to implement it. This option does not affect the public interface of any generated code; all method signatures remain the same. Furthermore, thread-safety of the interface is not affected by this option; const methods remain safe to call from multiple threads concurrently, while non-const methods continue to require exclusive access. Note that implementations may choose not to check required fields within a lazy sub-message. That is, calling IsInitialized() on the outer message may return true even if the inner message has missing required fields. This is necessary because otherwise the inner message would have to be parsed in order to perform the check, defeating the purpose of lazy parsing. An implementation which chooses not to check required fields must be consistent about it. That is, for any particular sub-message, the implementation must either *always* check its required fields, or *never check its required fields, regardless of whether or not the message has been parsed. |
| deprecated | [bool](#bool) | optional | Is this field deprecated? Depending on the target platform, this can emit Deprecated annotations for accessors, or it will be completely ignored; in the very least, this is a formalization for deprecating fields. |
| weak | [bool](#bool) | optional | For Google-internal migration only. Do not use. |
| uninterpreted_option | [UninterpretedOption](#google.protobuf.UninterpretedOption) | repeated | The parser stores options it doesn&#39;t recognize here. See above. |






<a name="google.protobuf.FileDescriptorProto"/>

### FileDescriptorProto
Describes a complete .proto file.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| name | [string](#string) | optional | file name, relative to root of source tree |
| package | [string](#string) | optional | e.g. &#34;foo&#34;, &#34;foo.bar&#34;, etc. |
| dependency | [string](#string) | repeated | Names of files imported by this file. |
| public_dependency | [int32](#int32) | repeated | Indexes of the public imported files in the dependency list above. |
| weak_dependency | [int32](#int32) | repeated | Indexes of the weak imported files in the dependency list. For Google-internal migration only. Do not use. |
| message_type | [DescriptorProto](#google.protobuf.DescriptorProto) | repeated | All top-level definitions in this file. |
| enum_type | [EnumDescriptorProto](#google.protobuf.EnumDescriptorProto) | repeated |  |
| service | [ServiceDescriptorProto](#google.protobuf.ServiceDescriptorProto) | repeated |  |
| extension | [FieldDescriptorProto](#google.protobuf.FieldDescriptorProto) | repeated |  |
| options | [FileOptions](#google.protobuf.FileOptions) | optional |  |
| source_code_info | [SourceCodeInfo](#google.protobuf.SourceCodeInfo) | optional | This field contains optional information about the original source code. You may safely remove this entire field without harming runtime functionality of the descriptors -- the information is needed only by development tools. |
| syntax | [string](#string) | optional | The syntax of the proto file. The supported values are &#34;proto2&#34; and &#34;proto3&#34;. |






<a name="google.protobuf.FileDescriptorSet"/>

### FileDescriptorSet
The protocol compiler can output a FileDescriptorSet containing the .proto
files it parses.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| file | [FileDescriptorProto](#google.protobuf.FileDescriptorProto) | repeated |  |






<a name="google.protobuf.FileOptions"/>

### FileOptions



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| java_package | [string](#string) | optional | Sets the Java package where classes generated from this .proto will be placed. By default, the proto package is used, but this is often inappropriate because proto packages do not normally start with backwards domain names. |
| java_outer_classname | [string](#string) | optional | If set, all the classes from the .proto file are wrapped in a single outer class with the given name. This applies to both Proto1 (equivalent to the old &#34;--one_java_file&#34; option) and Proto2 (where a .proto always translates to a single class, but you may want to explicitly choose the class name). |
| java_multiple_files | [bool](#bool) | optional | If set true, then the Java code generator will generate a separate .java file for each top-level message, enum, and service defined in the .proto file. Thus, these types will *not* be nested inside the outer class named by java_outer_classname. However, the outer class will still be generated to contain the file&#39;s getDescriptor() method as well as any top-level extensions defined in the file. |
| java_generate_equals_and_hash | [bool](#bool) | optional | This option does nothing. |
| java_string_check_utf8 | [bool](#bool) | optional | If set true, then the Java2 code generator will generate code that throws an exception whenever an attempt is made to assign a non-UTF-8 byte sequence to a string field. Message reflection will do the same. However, an extension field still accepts non-UTF-8 byte sequences. This option has no effect on when used with the lite runtime. |
| optimize_for | [FileOptions.OptimizeMode](#google.protobuf.FileOptions.OptimizeMode) | optional |  |
| go_package | [string](#string) | optional | Sets the Go package where structs generated from this .proto will be placed. If omitted, the Go package will be derived from the following: - The basename of the package import path, if provided. - Otherwise, the package statement in the .proto file, if present. - Otherwise, the basename of the .proto file, without extension. |
| cc_generic_services | [bool](#bool) | optional | Should generic services be generated in each language? &#34;Generic&#34; services are not specific to any particular RPC system. They are generated by the main code generators in each language (without additional plugins). Generic services were the only kind of service generation supported by early versions of google.protobuf. Generic services are now considered deprecated in favor of using plugins that generate code specific to your particular RPC system. Therefore, these default to false. Old code which depends on generic services should explicitly set them to true. |
| java_generic_services | [bool](#bool) | optional |  |
| py_generic_services | [bool](#bool) | optional |  |
| deprecated | [bool](#bool) | optional | Is this file deprecated? Depending on the target platform, this can emit Deprecated annotations for everything in the file, or it will be completely ignored; in the very least, this is a formalization for deprecating files. |
| cc_enable_arenas | [bool](#bool) | optional | Enables the use of arenas for the proto messages in this file. This applies only to generated classes for C&#43;&#43;. |
| objc_class_prefix | [string](#string) | optional | Sets the objective c class prefix which is prepended to all objective c generated classes from this .proto. There is no default. |
| csharp_namespace | [string](#string) | optional | Namespace for generated classes; defaults to the package. |
| swift_prefix | [string](#string) | optional | By default Swift generators will take the proto package and CamelCase it replacing &#39;.&#39; with underscore and use that to prefix the types/symbols defined. When this options is provided, they will use this value instead to prefix the types/symbols defined. |
| php_class_prefix | [string](#string) | optional | Sets the php class prefix which is prepended to all php generated classes from this .proto. Default is empty. |
| uninterpreted_option | [UninterpretedOption](#google.protobuf.UninterpretedOption) | repeated | The parser stores options it doesn&#39;t recognize here. See above. |






<a name="google.protobuf.GeneratedCodeInfo"/>

### GeneratedCodeInfo
Describes the relationship between generated code and its original source
file. A GeneratedCodeInfo message is associated with only one generated
source file, but may contain references to different source .proto files.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| annotation | [GeneratedCodeInfo.Annotation](#google.protobuf.GeneratedCodeInfo.Annotation) | repeated | An Annotation connects some span of text in generated code to an element of its generating .proto file. |






<a name="google.protobuf.GeneratedCodeInfo.Annotation"/>

### GeneratedCodeInfo.Annotation



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| path | [int32](#int32) | repeated | Identifies the element in the original source .proto file. This field is formatted the same as SourceCodeInfo.Location.path. |
| source_file | [string](#string) | optional | Identifies the filesystem path to the original source .proto. |
| begin | [int32](#int32) | optional | Identifies the starting offset in bytes in the generated code that relates to the identified object. |
| end | [int32](#int32) | optional | Identifies the ending offset in bytes in the generated code that relates to the identified offset. The end offset should be one past the last relevant byte (so the length of the text = end - begin). |






<a name="google.protobuf.MessageOptions"/>

### MessageOptions



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| message_set_wire_format | [bool](#bool) | optional | Set true to use the old proto1 MessageSet wire format for extensions. This is provided for backwards-compatibility with the MessageSet wire format. You should not use this for any other reason: It&#39;s less efficient, has fewer features, and is more complicated. The message must be defined exactly as follows: message Foo { option message_set_wire_format = true; extensions 4 to max; } Note that the message cannot have any defined fields; MessageSets only have extensions. All extensions of your type must be singular messages; e.g. they cannot be int32s, enums, or repeated messages. Because this is an option, the above two restrictions are not enforced by the protocol compiler. |
| no_standard_descriptor_accessor | [bool](#bool) | optional | Disables the generation of the standard &#34;descriptor()&#34; accessor, which can conflict with a field of the same name. This is meant to make migration from proto1 easier; new code should avoid fields named &#34;descriptor&#34;. |
| deprecated | [bool](#bool) | optional | Is this message deprecated? Depending on the target platform, this can emit Deprecated annotations for the message, or it will be completely ignored; in the very least, this is a formalization for deprecating messages. |
| map_entry | [bool](#bool) | optional | Whether the message is an automatically generated map entry type for the maps field. For maps fields: map&lt;KeyType, ValueType&gt; map_field = 1; The parsed descriptor looks like: message MapFieldEntry { option map_entry = true; optional KeyType key = 1; optional ValueType value = 2; } repeated MapFieldEntry map_field = 1; Implementations may choose not to generate the map_entry=true message, but use a native map in the target language to hold the keys and values. The reflection APIs in such implementions still need to work as if the field is a repeated message field. NOTE: Do not set the option in .proto files. Always use the maps syntax instead. The option should only be implicitly set by the proto compiler parser. |
| uninterpreted_option | [UninterpretedOption](#google.protobuf.UninterpretedOption) | repeated | The parser stores options it doesn&#39;t recognize here. See above. |






<a name="google.protobuf.MethodDescriptorProto"/>

### MethodDescriptorProto
Describes a method of a service.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| name | [string](#string) | optional |  |
| input_type | [string](#string) | optional | Input and output type names. These are resolved in the same way as FieldDescriptorProto.type_name, but must refer to a message type. |
| output_type | [string](#string) | optional |  |
| options | [MethodOptions](#google.protobuf.MethodOptions) | optional |  |
| client_streaming | [bool](#bool) | optional | Identifies if client streams multiple client messages |
| server_streaming | [bool](#bool) | optional | Identifies if server streams multiple server messages |






<a name="google.protobuf.MethodOptions"/>

### MethodOptions



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| deprecated | [bool](#bool) | optional | Is this method deprecated? Depending on the target platform, this can emit Deprecated annotations for the method, or it will be completely ignored; in the very least, this is a formalization for deprecating methods. |
| idempotency_level | [MethodOptions.IdempotencyLevel](#google.protobuf.MethodOptions.IdempotencyLevel) | optional |  |
| uninterpreted_option | [UninterpretedOption](#google.protobuf.UninterpretedOption) | repeated | The parser stores options it doesn&#39;t recognize here. See above. |






<a name="google.protobuf.OneofDescriptorProto"/>

### OneofDescriptorProto
Describes a oneof.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| name | [string](#string) | optional |  |
| options | [OneofOptions](#google.protobuf.OneofOptions) | optional |  |






<a name="google.protobuf.OneofOptions"/>

### OneofOptions



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| uninterpreted_option | [UninterpretedOption](#google.protobuf.UninterpretedOption) | repeated | The parser stores options it doesn&#39;t recognize here. See above. |






<a name="google.protobuf.ServiceDescriptorProto"/>

### ServiceDescriptorProto
Describes a service.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| name | [string](#string) | optional |  |
| method | [MethodDescriptorProto](#google.protobuf.MethodDescriptorProto) | repeated |  |
| options | [ServiceOptions](#google.protobuf.ServiceOptions) | optional |  |






<a name="google.protobuf.ServiceOptions"/>

### ServiceOptions



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| deprecated | [bool](#bool) | optional | Is this service deprecated? Depending on the target platform, this can emit Deprecated annotations for the service, or it will be completely ignored; in the very least, this is a formalization for deprecating services. |
| uninterpreted_option | [UninterpretedOption](#google.protobuf.UninterpretedOption) | repeated | The parser stores options it doesn&#39;t recognize here. See above. |






<a name="google.protobuf.SourceCodeInfo"/>

### SourceCodeInfo
Encapsulates information about the original source file from which a
FileDescriptorProto was generated.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| location | [SourceCodeInfo.Location](#google.protobuf.SourceCodeInfo.Location) | repeated | A Location identifies a piece of source code in a .proto file which corresponds to a particular definition. This information is intended to be useful to IDEs, code indexers, documentation generators, and similar tools. For example, say we have a file like: message Foo { optional string foo = 1; } Let&#39;s look at just the field definition: optional string foo = 1; ^ ^^ ^^ ^ ^^^ a bc de f ghi We have the following locations: span path represents [a,i) [ 4, 0, 2, 0 ] The whole field definition. [a,b) [ 4, 0, 2, 0, 4 ] The label (optional). [c,d) [ 4, 0, 2, 0, 5 ] The type (string). [e,f) [ 4, 0, 2, 0, 1 ] The name (foo). [g,h) [ 4, 0, 2, 0, 3 ] The number (1). Notes: - A location may refer to a repeated field itself (i.e. not to any particular index within it). This is used whenever a set of elements are logically enclosed in a single code segment. For example, an entire extend block (possibly containing multiple extension definitions) will have an outer location whose path refers to the &#34;extensions&#34; repeated field without an index. - Multiple locations may have the same path. This happens when a single logical declaration is spread out across multiple places. The most obvious example is the &#34;extend&#34; block again -- there may be multiple extend blocks in the same scope, each of which will have the same path. - A location&#39;s span is not always a subset of its parent&#39;s span. For example, the &#34;extendee&#34; of an extension declaration appears at the beginning of the &#34;extend&#34; block and is shared by all extensions within the block. - Just because a location&#39;s span is a subset of some other location&#39;s span does not mean that it is a descendent. For example, a &#34;group&#34; defines both a type and a field in a single declaration. Thus, the locations corresponding to the type and field and their components will overlap. - Code which tries to interpret locations should probably be designed to ignore those that it doesn&#39;t understand, as more types of locations could be recorded in the future. |






<a name="google.protobuf.SourceCodeInfo.Location"/>

### SourceCodeInfo.Location



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| path | [int32](#int32) | repeated | Identifies which part of the FileDescriptorProto was defined at this location. Each element is a field number or an index. They form a path from the root FileDescriptorProto to the place where the definition. For example, this path: [ 4, 3, 2, 7, 1 ] refers to: file.message_type(3) // 4, 3 .field(7) // 2, 7 .name() // 1 This is because FileDescriptorProto.message_type has field number 4: repeated DescriptorProto message_type = 4; and DescriptorProto.field has field number 2: repeated FieldDescriptorProto field = 2; and FieldDescriptorProto.name has field number 1: optional string name = 1; Thus, the above path gives the location of a field name. If we removed the last element: [ 4, 3, 2, 7 ] this path refers to the whole field declaration (from the beginning of the label to the terminating semicolon). |
| span | [int32](#int32) | repeated | Always has exactly three or four elements: start line, start column, end line (optional, otherwise assumed same as start line), end column. These are packed into a single field for efficiency. Note that line and column numbers are zero-based -- typically you will want to add 1 to each before displaying to a user. |
| leading_comments | [string](#string) | optional | If this SourceCodeInfo represents a complete declaration, these are any comments appearing before and after the declaration which appear to be attached to the declaration. A series of line comments appearing on consecutive lines, with no other tokens appearing on those lines, will be treated as a single comment. leading_detached_comments will keep paragraphs of comments that appear before (but not connected to) the current element. Each paragraph, separated by empty lines, will be one comment element in the repeated field. Only the comment content is provided; comment markers (e.g. //) are stripped out. For block comments, leading whitespace and an asterisk will be stripped from the beginning of each line other than the first. Newlines are included in the output. Examples: optional int32 foo = 1; // Comment attached to foo. Comment attached to bar. optional int32 bar = 2; optional string baz = 3; Comment attached to baz. Another line attached to baz. Comment attached to qux. Another line attached to qux. optional double qux = 4; Detached comment for corge. This is not leading or trailing comments to qux or corge because there are blank lines separating it from both. Detached comment for corge paragraph 2. optional string corge = 5; Block comment attached to corge. Leading asterisks will be removed. Block comment attached to grault. optional int32 grault = 6; ignored detached comments. |
| trailing_comments | [string](#string) | optional |  |
| leading_detached_comments | [string](#string) | repeated |  |






<a name="google.protobuf.UninterpretedOption"/>

### UninterpretedOption
A message representing a option the parser does not recognize. This only
appears in options protos created by the compiler::Parser class.
DescriptorPool resolves these when building Descriptor objects. Therefore,
options protos in descriptor objects (e.g. returned by Descriptor::options(),
or produced by Descriptor::CopyTo()) will never have UninterpretedOptions
in them.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| name | [UninterpretedOption.NamePart](#google.protobuf.UninterpretedOption.NamePart) | repeated |  |
| identifier_value | [string](#string) | optional | The value of the uninterpreted option, in whatever type the tokenizer identified it as during parsing. Exactly one of these should be set. |
| positive_int_value | [uint64](#uint64) | optional |  |
| negative_int_value | [int64](#int64) | optional |  |
| double_value | [double](#double) | optional |  |
| string_value | [bytes](#bytes) | optional |  |
| aggregate_value | [string](#string) | optional |  |






<a name="google.protobuf.UninterpretedOption.NamePart"/>

### UninterpretedOption.NamePart
The name of the uninterpreted option.  Each string represents a segment in
a dot-separated name.  is_extension is true iff a segment represents an
extension (denoted with parentheses in options specs in .proto files).
E.g.,{ [&#34;foo&#34;, false], [&#34;bar.baz&#34;, true], [&#34;qux&#34;, false] } represents
&#34;foo.(bar.baz).qux&#34;.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| name_part | [string](#string) | required |  |
| is_extension | [bool](#bool) | required |  |





 


<a name="google.protobuf.FieldDescriptorProto.Label"/>

### FieldDescriptorProto.Label


| Name | Number | Description |
| ---- | ------ | ----------- |
| LABEL_OPTIONAL | 1 | 0 is reserved for errors |
| LABEL_REQUIRED | 2 |  |
| LABEL_REPEATED | 3 |  |



<a name="google.protobuf.FieldDescriptorProto.Type"/>

### FieldDescriptorProto.Type


| Name | Number | Description |
| ---- | ------ | ----------- |
| TYPE_DOUBLE | 1 | 0 is reserved for errors. Order is weird for historical reasons. |
| TYPE_FLOAT | 2 |  |
| TYPE_INT64 | 3 | Not ZigZag encoded. Negative numbers take 10 bytes. Use TYPE_SINT64 if negative values are likely. |
| TYPE_UINT64 | 4 |  |
| TYPE_INT32 | 5 | Not ZigZag encoded. Negative numbers take 10 bytes. Use TYPE_SINT32 if negative values are likely. |
| TYPE_FIXED64 | 6 |  |
| TYPE_FIXED32 | 7 |  |
| TYPE_BOOL | 8 |  |
| TYPE_STRING | 9 |  |
| TYPE_GROUP | 10 | Tag-delimited aggregate. Group type is deprecated and not supported in proto3. However, Proto3 implementations should still be able to parse the group wire format and treat group fields as unknown fields. |
| TYPE_MESSAGE | 11 | Length-delimited aggregate. |
| TYPE_BYTES | 12 | New in version 2. |
| TYPE_UINT32 | 13 |  |
| TYPE_ENUM | 14 |  |
| TYPE_SFIXED32 | 15 |  |
| TYPE_SFIXED64 | 16 |  |
| TYPE_SINT32 | 17 | Uses ZigZag encoding. |
| TYPE_SINT64 | 18 | Uses ZigZag encoding. |



<a name="google.protobuf.FieldOptions.CType"/>

### FieldOptions.CType


| Name | Number | Description |
| ---- | ------ | ----------- |
| STRING | 0 | Default mode. |
| CORD | 1 |  |
| STRING_PIECE | 2 |  |



<a name="google.protobuf.FieldOptions.JSType"/>

### FieldOptions.JSType


| Name | Number | Description |
| ---- | ------ | ----------- |
| JS_NORMAL | 0 | Use the default type. |
| JS_STRING | 1 | Use JavaScript strings. |
| JS_NUMBER | 2 | Use JavaScript numbers. |



<a name="google.protobuf.FileOptions.OptimizeMode"/>

### FileOptions.OptimizeMode
Generated classes can be optimized for speed or code size.

| Name | Number | Description |
| ---- | ------ | ----------- |
| SPEED | 1 | Generate complete code for parsing, serialization, |
| CODE_SIZE | 2 | etc. Use ReflectionOps to implement these methods. |
| LITE_RUNTIME | 3 | Generate code using MessageLite and the lite runtime. |



<a name="google.protobuf.MethodOptions.IdempotencyLevel"/>

### MethodOptions.IdempotencyLevel
Is this method side-effect-free (or safe in HTTP parlance), or idempotent,
or neither? HTTP based RPC implementation may choose GET verb for safe
methods, and PUT verb for idempotent methods instead of the default POST.

| Name | Number | Description |
| ---- | ------ | ----------- |
| IDEMPOTENCY_UNKNOWN | 0 |  |
| NO_SIDE_EFFECTS | 1 | implies idempotent |
| IDEMPOTENT | 2 | idempotent, but may have side effects |


 

 

 



<a name="annotations.proto"/>
<p align="right"><a href="#top">Top</a></p>

## annotations.proto


 

 


<a name="annotations.proto-extensions"/>

### File-level Extensions
| Extension | Type | Base | Number | Description |
| --------- | ---- | ---- | ------ | ----------- |
| http | HttpRule | google.protobuf.MethodOptions | 72295728 | See `HttpRule`. |

 

 



<a name="common.proto"/>
<p align="right"><a href="#top">Top</a></p>

## common.proto



<a name="spire.common.AttestationData"/>

### AttestationData
A type which contains attestation data for specific platform.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| type | [string](#string) |  | Type of attestation to perform. |
| data | [bytes](#bytes) |  | The attestation data. |






<a name="spire.common.Bundle"/>

### Bundle



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| trust_domain_id | [string](#string) |  | the SPIFFE ID of the trust domain the bundle belongs to |
| root_cas | [Certificate](#spire.common.Certificate) | repeated | list of root CA certificates |
| jwt_signing_keys | [PublicKey](#spire.common.PublicKey) | repeated | list of JWT signing keys |






<a name="spire.common.Certificate"/>

### Certificate
Certificate represents a ASN.1/DER encoded X509 certificate


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| der_bytes | [bytes](#bytes) |  |  |






<a name="spire.common.Empty"/>

### Empty
Represents an empty message






<a name="spire.common.PublicKey"/>

### PublicKey
PublicKey represents a PKIX encoded public key


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| pkix_bytes | [bytes](#bytes) |  | PKIX encoded key data |
| kid | [string](#string) |  | key identifier |
| not_after | [int64](#int64) |  | not after (seconds since unix epoch, 0 means &#34;never expires&#34;) |






<a name="spire.common.RegistrationEntries"/>

### RegistrationEntries
A list of registration entries.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| entries | [RegistrationEntry](#spire.common.RegistrationEntry) | repeated | A list of RegistrationEntry. |






<a name="spire.common.RegistrationEntry"/>

### RegistrationEntry
This is a curated record that the Server uses to set up and
manage the various registered nodes and workloads that are controlled by it.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| selectors | [Selector](#spire.common.Selector) | repeated | A list of selectors. |
| parent_id | [string](#string) |  | The SPIFFE ID of an entity that is authorized to attest the validity of a selector |
| spiffe_id | [string](#string) |  | The SPIFFE ID is a structured string used to identify a resource or caller. It is defined as a URI comprising a “trust domain” and an associated path. |
| ttl | [int32](#int32) |  | Time to live. |
| federates_with | [string](#string) | repeated | A list of federated trust domain SPIFFE IDs. |
| entry_id | [string](#string) |  | Entry ID |
| admin | [bool](#bool) |  | Whether or not the workload is an admin workload. Admin workloads can use their SVID&#39;s to authenticate with the Registration API, for example. |






<a name="spire.common.Selector"/>

### Selector
A type which describes the conditions under which a registration
entry is matched.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| type | [string](#string) |  | A selector type represents the type of attestation used in attesting the entity (Eg: AWS, K8). |
| value | [string](#string) |  | The value to be attested. |






<a name="spire.common.Selectors"/>

### Selectors
Represents a type with a list of Selector.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| entries | [Selector](#spire.common.Selector) | repeated | A list of Selector. |





 

 

 

 



<a name="registration.proto"/>
<p align="right"><a href="#top">Top</a></p>

## registration.proto



<a name="spire.api.registration.Bundle"/>

### Bundle
CA Bundle of the server


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| DEPRECATED_ca_certs | [bytes](#bytes) |  | ASN.1 DER data of the bundle (deprecated). |
| bundle | [.spire.common.Bundle](#spire.api.registration..spire.common.Bundle) |  | Common bundle format |






<a name="spire.api.registration.DeleteFederatedBundleRequest"/>

### DeleteFederatedBundleRequest



| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| id | [string](#string) |  |  |
| mode | [DeleteFederatedBundleRequest.Mode](#spire.api.registration.DeleteFederatedBundleRequest.Mode) |  |  |






<a name="spire.api.registration.FederatedBundle"/>

### FederatedBundle
A CA bundle for a different Trust Domain than the one used and managed by the Server.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| DEPRECATED_spiffe_id | [string](#string) |  | A SPIFFE ID that has a Federated Bundle (deprecated) |
| DEPRECATED_ca_certs | [bytes](#bytes) |  | ASN.1 DER data of the bundle (deprecated) |
| bundle | [.spire.common.Bundle](#spire.api.registration..spire.common.Bundle) |  | Common bundle format |






<a name="spire.api.registration.FederatedBundleID"/>

### FederatedBundleID
A type that represents a federated bundle id.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| id | [string](#string) |  | SPIFFE ID of the federated bundle |






<a name="spire.api.registration.JoinToken"/>

### JoinToken
JoinToken message is used for registering a new token


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| token | [string](#string) |  | The join token. If not set, one will be generated |
| ttl | [int32](#int32) |  | TTL in seconds |






<a name="spire.api.registration.ParentID"/>

### ParentID
A type that represents a parent Id.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| id | [string](#string) |  | ParentId. |






<a name="spire.api.registration.RegistrationEntryID"/>

### RegistrationEntryID
A type that represents the id of an entry.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| id | [string](#string) |  | RegistrationEntryID. |






<a name="spire.api.registration.SpiffeID"/>

### SpiffeID
A type that represents a SPIFFE Id.


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| id | [string](#string) |  | SpiffeId. |






<a name="spire.api.registration.UpdateEntryRequest"/>

### UpdateEntryRequest
A type used to update registration entries


| Field | Type | Label | Description |
| ----- | ---- | ----- | ----------- |
| entry | [.spire.common.RegistrationEntry](#spire.api.registration..spire.common.RegistrationEntry) |  | Registration entry to update |





 


<a name="spire.api.registration.DeleteFederatedBundleRequest.Mode"/>

### DeleteFederatedBundleRequest.Mode
Mode controls the delete behavior if there are other records
associated with the bundle (e.g. registration entries).

| Name | Number | Description |
| ---- | ------ | ----------- |
| RESTRICT | 0 | RESTRICT prevents the bundle from being deleted in the presence of associated entries |
| DELETE | 1 | DELETE deletes the bundle and associated entries |
| DISSOCIATE | 2 | DISSOCIATE deletes the bundle and dissociates associated entries |


 

 


<a name="spire.api.registration.Registration"/>

### Registration


| Method Name | Request Type | Response Type | Description |
| ----------- | ------------ | ------------- | ------------|
| CreateEntry | [spire.common.RegistrationEntry](#spire.common.RegistrationEntry) | [RegistrationEntryID](#spire.common.RegistrationEntry) | Creates an entry in the Registration table, used to assign SPIFFE IDs to nodes and workloads. |
| DeleteEntry | [RegistrationEntryID](#spire.api.registration.RegistrationEntryID) | [spire.common.RegistrationEntry](#spire.api.registration.RegistrationEntryID) | Deletes an entry and returns the deleted entry. |
| FetchEntry | [RegistrationEntryID](#spire.api.registration.RegistrationEntryID) | [spire.common.RegistrationEntry](#spire.api.registration.RegistrationEntryID) | Retrieve a specific registered entry. |
| FetchEntries | [spire.common.Empty](#spire.common.Empty) | [spire.common.RegistrationEntries](#spire.common.Empty) | Retrieve all registered entries. |
| UpdateEntry | [UpdateEntryRequest](#spire.api.registration.UpdateEntryRequest) | [spire.common.RegistrationEntry](#spire.api.registration.UpdateEntryRequest) | Updates a specific registered entry. |
| ListByParentID | [ParentID](#spire.api.registration.ParentID) | [spire.common.RegistrationEntries](#spire.api.registration.ParentID) | Returns all the Entries associated with the ParentID value. |
| ListBySelector | [spire.common.Selector](#spire.common.Selector) | [spire.common.RegistrationEntries](#spire.common.Selector) | Returns all the entries associated with a selector value. |
| ListBySpiffeID | [SpiffeID](#spire.api.registration.SpiffeID) | [spire.common.RegistrationEntries](#spire.api.registration.SpiffeID) | Return all registration entries for which SPIFFE ID matches. |
| CreateFederatedBundle | [FederatedBundle](#spire.api.registration.FederatedBundle) | [spire.common.Empty](#spire.api.registration.FederatedBundle) | Creates an entry in the Federated bundle table to store the mappings of Federated SPIFFE IDs and their associated CA bundle. |
| FetchFederatedBundle | [FederatedBundleID](#spire.api.registration.FederatedBundleID) | [FederatedBundle](#spire.api.registration.FederatedBundleID) | Retrieves a single federated bundle |
| ListFederatedBundles | [spire.common.Empty](#spire.common.Empty) | [FederatedBundle](#spire.common.Empty) | Retrieves Federated bundles for all the Federated SPIFFE IDs. |
| UpdateFederatedBundle | [FederatedBundle](#spire.api.registration.FederatedBundle) | [spire.common.Empty](#spire.api.registration.FederatedBundle) | Updates a particular Federated Bundle. Useful for rotation. |
| DeleteFederatedBundle | [DeleteFederatedBundleRequest](#spire.api.registration.DeleteFederatedBundleRequest) | [spire.common.Empty](#spire.api.registration.DeleteFederatedBundleRequest) | Delete a particular Federated Bundle. Used to destroy inter-domain trust. |
| CreateJoinToken | [JoinToken](#spire.api.registration.JoinToken) | [JoinToken](#spire.api.registration.JoinToken) | Create a new join token |
| FetchBundle | [spire.common.Empty](#spire.common.Empty) | [Bundle](#spire.common.Empty) | Retrieves the CA bundle. |

 



## Scalar Value Types

| .proto Type | Notes | C++ Type | Java Type | Python Type |
| ----------- | ----- | -------- | --------- | ----------- |
| <a name="double" /> double |  | double | double | float |
| <a name="float" /> float |  | float | float | float |
| <a name="int32" /> int32 | Uses variable-length encoding. Inefficient for encoding negative numbers – if your field is likely to have negative values, use sint32 instead. | int32 | int | int |
| <a name="int64" /> int64 | Uses variable-length encoding. Inefficient for encoding negative numbers – if your field is likely to have negative values, use sint64 instead. | int64 | long | int/long |
| <a name="uint32" /> uint32 | Uses variable-length encoding. | uint32 | int | int/long |
| <a name="uint64" /> uint64 | Uses variable-length encoding. | uint64 | long | int/long |
| <a name="sint32" /> sint32 | Uses variable-length encoding. Signed int value. These more efficiently encode negative numbers than regular int32s. | int32 | int | int |
| <a name="sint64" /> sint64 | Uses variable-length encoding. Signed int value. These more efficiently encode negative numbers than regular int64s. | int64 | long | int/long |
| <a name="fixed32" /> fixed32 | Always four bytes. More efficient than uint32 if values are often greater than 2^28. | uint32 | int | int |
| <a name="fixed64" /> fixed64 | Always eight bytes. More efficient than uint64 if values are often greater than 2^56. | uint64 | long | int/long |
| <a name="sfixed32" /> sfixed32 | Always four bytes. | int32 | int | int |
| <a name="sfixed64" /> sfixed64 | Always eight bytes. | int64 | long | int/long |
| <a name="bool" /> bool |  | bool | boolean | boolean |
| <a name="string" /> string | A string must always contain UTF-8 encoded or 7-bit ASCII text. | string | String | str/unicode |
| <a name="bytes" /> bytes | May contain any arbitrary sequence of bytes. | string | ByteString | str |

