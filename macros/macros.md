###Macros

What do macro's do?

Wel macro's are macro's, little pieces of code that can be processed quickly.

What special macro's are there?

Some special macro's that are used to manipulate memory in various parts

GET_BUFFER
GET_UNIT

(Not complete list from macros.reds)
#define TYPE_OF(value)		(value/header and get-type-mask)
#define TUPLE_SIZE?(value)	(value/header >> 19 and 15)
#define GET_TUPLE_ARRAY(tp) [(as byte-ptr! tp) + 4]
#define SET_TUPLE_SIZE(t n) [t/header: t/header and FF87FFFFh or (n << 19)]
#define GET_BUFFER(series)  (as series! series/node/value)
#define GET_UNIT(series)	(series/flags and get-unit-mask)
#define ALLOC_TAIL(series)	[alloc-at-tail series]
#define FLAG_SET?(flag)		(flags and flag <> 0)
#define OPTION?(ref-ptr)	(ref-ptr > stack/arguments)	;-- a bit inelegant, but saves a lot of code
#define ON_STACK?(ctx)		(ctx/header and flag-series-stk <> 0)
#define EQUAL_SYMBOLS?(a b) ((symbol/resolve a) = (symbol/resolve b))
#define EQUAL_WORDS?(a b) 	((symbol/resolve a/symbol) = (symbol/resolve b/symbol))
#define TO_CTX(node)		(as red-context! ((as series! node/value) + 1))
#define GET_CTX(obj)		(as red-context! ((as series! obj/ctx/value) + 1))
#define FLAG_NOT?(s)		(s/flags and flag-bitset-not <> 0)
#define SET_RETURN(value)	[stack/set-last as red-value! value]
#define TO_ERROR(cat id)	[#in system/catalog/errors cat #in system/catalog/errors/cat id]

#define WHITE_CHAR?(char)	[
	any [
		all [0 < char char < 33]			;-- All white chars: NL, CR, BS, etc...
		char = 133							;-- #"^(85)"
		char = 160							;-- #"^(A0)"
		char = 5760							;-- #"^(1680)"
		char = 6158							;-- #"^(180E)"
		all [8192 <= char char <= 8202]		;-- #"^(2000)" - #"^(200A)"
		char = 8232							;-- #"^(2028)"
		char = 8233							;-- #"^(2029)"
		char = 8239							;-- #"^(202F)"
		char = 8287							;-- #"^(205F)"
		char = 12288						;-- #"^(3000)"
	]
]

#define SPACE_CHAR?(char)	[
	any [
		char = 32							;-- #" "
		char = 9							;-- #"^-"
		char = 133							;-- #"^(85)"
		char = 160							;-- #"^(A0)"
		char = 5760							;-- #"^(1680)"
		char = 6158							;-- #"^(180E)"
		all [8192 <= char char <= 8202]		;-- #"^(2000)" - #"^(200A)"
		char = 8232							;-- #"^(2028)"
		char = 8233							;-- #"^(2029)"
		char = 8239							;-- #"^(202F)"
		char = 8287							;-- #"^(205F)"
		char = 12288						;-- #"^(3000)"
	]
]

#define ANY_SERIES?(type)	[
	any [
		type = TYPE_BLOCK
		type = TYPE_PAREN
		type = TYPE_PATH
		type = TYPE_LIT_PATH
		type = TYPE_SET_PATH
		type = TYPE_GET_PATH
		type = TYPE_STRING
		type = TYPE_FILE
		type = TYPE_URL
		type = TYPE_BINARY
		type = TYPE_IMAGE
	]
]

#define ANY_BLOCK?(type)	[
	any [									;@@ replace with ANY_BLOCK?
		type = TYPE_BLOCK
		type = TYPE_PAREN
		type = TYPE_PATH
		type = TYPE_GET_PATH
		type = TYPE_SET_PATH
		type = TYPE_LIT_PATH
	]
]

#define BS_SET_BIT(array bit)  [
	pos: array + (bit >> 3)
	pos/value: pos/value or (as-byte 128 >> (bit and 7))
]

#define BS_CLEAR_BIT(array bit)  [
	pos: array + (bit >> 3)
	pos/value: pos/value and (as-byte 128 >> (bit and 7) xor 255)
]

#define BS_TEST_BIT(array bit set?)  [
	pos: array + (bit >> 3)
	set?: pos/value and (as-byte 128 >> (bit and 7)) <> null-byte
]

#define BS_TEST_BIT_ALT(ts bit) [
	pos: ((as byte-ptr! ts) + 4) + (bit >> 3)
	pos/value and (as-byte 128 >> (bit and 7)) <> null-byte
]

#define BS_PROCESS_SET_VIRTUAL(bs bit) [
	either not? [
		if virtual-bit? bs bit [return 1]
	][
		pbits: bound-check bs bit
	]
]

#define BS_PROCESS_CLEAR_VIRTUAL(bs bit) [
	either not? [
		pbits: bound-check bs bit
	][
		if virtual-bit? bs bit [return 0]
	]
]

#define ERR_INVALID_REFINEMENT_ARG(refine arg) [
	fire [
		TO_ERROR(script invalid-refine-arg)
		refine
		arg
	]
]

#define ERR_EXPECT_ARGUMENT(type arg-idx) [
	fire [
		TO_ERROR(script expect-arg)
		stack/get-call
		datatype/push type
		error/get-call-argument arg-idx
	]
]

#define --NOT_IMPLEMENTED--	[
	fire [TO_ERROR(internal not-done)]
]

#define RETURN_COMPARE_OTHER [
	return -2
]

#define CHECK_COMPARE_OTHER(type) [
	if all [
		TYPE_OF(str2) <> type
		any [
			all [
				op <> COMP_EQUAL
				op <> COMP_NOT_EQUAL
			]
			all [
				TYPE_OF(str2) <> TYPE_STRING		;@@ use ANY_STRING?
				TYPE_OF(str2) <> TYPE_FILE
				TYPE_OF(str2) <> TYPE_URL
			]
		]
	][RETURN_COMPARE_OTHER]
]

#define SIGN_COMPARE_RESULT(a b) [
	either a < b [-1][either a > b [1][0]]
]

#define IMAGE_WIDTH(size)  (size and FFFFh) 
#define IMAGE_HEIGHT(size) (size >> 16)

Where get-unit-mask		0000001Fh and others are defined in allocator.reds

Remember that this is code that helps compile for Red/System code is not supported as Red/System code.
For example red-string! is not a Red/System type but red-string! is used in natives.reds often.


