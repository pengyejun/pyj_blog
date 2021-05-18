### PyFloatObject
`float`作为`Python`中最简单的对象之一，但是“麻雀虽小，五脏俱全”，拥有对象的必要属性，`PyFloatObject`源码在`Include/floatobject.h`中，定义如下：

    typedef struct {
        PyObject_HEAD    /* PyObject结构体 */
        double ob_fval;  /* 存储浮点数的值 */
    } PyFloatObject;

### PyFloat_Type
与`float`实例对象不同，`float`类型对象全局唯一，可以作为全局变量定义，`Python`也是这样实现的，代码在`Objects/floatobject.c`中，定义如下：

    PyTypeObject PyFloat_Type = {
        PyVarObject_HEAD_INIT(&PyType_Type, 0) 
        "float", /* 类名 */
        sizeof(PyFloatObject),  
        0,
        (destructor)float_dealloc, /* tp_dealloc */
        ...
        (reprfunc)float_repr,  /* tp_repr */
        &float_as_number, /* tp_as_number，支持number相关方法 */
        0,                /* tp_as_sequence */
        0,                /* tp_as_mapping */
        (hashfunc)float_hash, /* tp_hash */
        0,                /* tp_call */
        (reprfunc)float_repr, /* tp_str */
        ...
        0,                /* tp_base */
        ...
        float_new,        /* tp_new */
    };
关键字段解析：
- tp_name 字段保存类型名称，常量`float`
- tp_dealloc、tp_init、tp_alloc 和 tp_new 字段是对象创建销毁相关函数
- tp_repr 字段是⽣成语法字符串表⽰形式的函数
- tp_str 字段是⽣成普通字符串表⽰形式的函数
- tp_as_number 字段是数值操作集
- tp_hash 字段是哈希值⽣成函数
- tp_new 创建`float`实例对象的方法

值得注意的是，这里的tp_base居然是空的，但是前文[PyBaseObject_Type](language/python/base/pyobject?id=pybaseobject_type类型之基)说到任何类都是集成自`PyBaseObject_Type`，但是我们通过`Python`解释器打印

    >>>float.__mro__
    >>>(<class 'float'>, <class >>>'object'>)

因此可以断定这个`float`是继承自`object`的，继续找代码，在`Objects/object.c`中发现了一段逻辑：

    if (PyType_Ready(&PyFloat_Type) < 0)
        Py_FatalError("Can't initialize float type");
    
    int
    PyType_Ready(PyTypeObject *type) {
        // ...
        base = type->tp_base;
        if (base == NULL && type != &PyBaseObject_Type) {
        base = type->tp_base = &PyBaseObject_Type;
        Py_INCREF(base); }
        // ...
    }

原来定义的`PyFloat_Type`是个半成品，`PyType_Ready`会把`PyFloat_Type`中的tp_base字段初始化为`PyBaseObject_Type`。

### 创建`float`实例对象的过程
创建实例对象时，`Python`先调用`type`类型的`tp_call`函数，`tp_call`函数进而调用`float`类型对象的`tp_new`方法创建实例对象，再调用`tp_init`函数对其进行初始化。

由于`float`是`Python`内置的简单类型对象，因此在创建`float`实例对象时，`Python`走了捷径，只调用`float`类型的`tp_new`方法。直接找到`float_new`代码的位置`Objects/floatobject.c`，代码如下：

    static PyObject *
    float_new(PyTypeObject *type, PyObject *args, PyObject *kwds)
    {
        PyObject *x = Py_False; /* Integer zero */
        static char *kwlist[] = {"x", 0};

        if (type != &PyFloat_Type)
            /* 创建继承float的类的实例对象 */
            return float_subtype_new(type, args, kwds);
        // 处理参数是否合法
        if (!PyArg_ParseTupleAndKeywords(args, kwds, "|O:float", kwlist, &x))
            return NULL;
        if (PyUnicode_CheckExact(x))
            /* 只能处理标准str类型，不能处理str子类 */
            return PyFloat_FromString(x);
        / *处理数值类型参数，包含int, float */
        return PyNumber_Float(x);
    }

float_subtype_new：处理float子类实例对象的创建，内部实现如下，截取主要信息
    
    float_subtype_new(PyTypeObject *type, PyObject *args, PyObject *kwds)
    {
        PyObject *tmp, *newobj;

        assert(PyType_IsSubtype(type, &PyFloat_Type));
        // 创建一个临时float实例对象
        tmp = float_new(&PyFloat_Type, args, kwds);
        if (tmp == NULL)
            return NULL;
        assert(PyFloat_Check(tmp));
        // 创建float子类实例对象
        newobj = type->tp_alloc(type, 0);
        if (newobj == NULL) {
            Py_DECREF(tmp);
            return NULL;
        }
        //将临时float实例对象的ob_fval值赋值给float子类实例对象
        ((PyFloatObject *)newobj)->ob_fval = ((PyFloatObject *)tmp)->ob_fval;
        // 销毁临时float实例对象
        Py_DECREF(tmp);
        return newobj;
    }

PyFloat_FromString：该方法实现较为复杂，涉及到`str`实例对象，后续再分析，这里抛出结论 `把整数或者浮点数的字符串转化为浮点数，再调用PyFloat_FromDouble函数生成float实例对象。例如可以转换123、123.4。但是意外的是它能解析1_2_3.4，查看源码后，发现把_字符当做了无意义字符，但是_字符左边必须为数字，目前还不太清楚为什么需要兼容_字符，但是生产环境建议别用这种骚操作！`

    >>>float("1_2_3.4")
    >>>123.4

PyNumber_Float：仍然截取内部实现主要信息，屏蔽一些不重要的代码

    PyObject *
    PyNumber_Float(PyObject *o)
    {
        PyNumberMethods *m;

        ...
        m = o->ob_type->tp_as_number;
        if (m && m->nb_float) { 
            // 处理实现了__float__魔法方法的类，并且返回值必须为float类型或者float子类
            PyObject *res = m->nb_float(o);
            double val;
            if (!res || PyFloat_CheckExact(res)) {
                return res;
            }
            // 判断是否为float或者float子类
            if (!PyFloat_Check(res)) {
                ...
                Py_DECREF(res);
                return NULL;
            }
            // 建议返回float，未来可能不接受返回float子类
            if (PyErr_WarnFormat(PyExc_DeprecationWarning, 1,
                    "%.50s.__float__ returned non-float (type %.50s).  "
                    "The ability to return an instance of a strict subclass of float "
                    "is deprecated, and may be removed in a future version of Python.",
                    o->ob_type->tp_name, res->ob_type->tp_name)) {
                Py_DECREF(res);
                return NULL;
            }
            val = PyFloat_AS_DOUBLE(res);
            Py_DECREF(res);
            return PyFloat_FromDouble(val);
        }
        if (PyFloat_Check(o)) { 
            // 处理参数为float或者float子类
            return PyFloat_FromDouble(PyFloat_AS_DOUBLE(o));
        }
        return PyFloat_FromString(o);
    }

PyFloat_FromDouble：此函数为生成float浮点对象的核心函数

    PyObject *
    PyFloat_FromDouble(double fval)
    {
        // free_list 空闲链表，float实例对象缓存链
        PyFloatObject *op = free_list;
        if (op != NULL) {
            // 找到空闲对象，将free_list指针移动到下一个节点
            free_list = (PyFloatObject *) Py_TYPE(op);
            // 空闲对象计数减一
            numfree--;
        } else {
            // 找不到空闲对象，则需要重新申请内存
            op = (PyFloatObject*) PyObject_MALLOC(sizeof(PyFloatObject));
            if (!op)
                return PyErr_NoMemory();
        }
        /* 重新初始化float实例对象 */
        (void)PyObject_INIT(op, &PyFloat_Type);
        // 给float实例对象赋值
        op->ob_fval = fval;
        return (PyObject *) op;
    }

这里涉及到了free_list，我们来深入研究一下此数据结构，找到定义的地方

    #ifndef PyFloat_MAXFREELIST
    #define PyFloat_MAXFREELIST    100
    #endif
    static int numfree = 0;
    static PyFloatObject *free_list = NULL;
free_list是指向空闲单链表头节点的指针，默认情况下空闲单链表最大长度为100，numfree表示当前空闲的对象个数，继续看空闲单链表数据结构

    PyFloatObject.ob_type -> PyFloatObject.ob_type -> PyFloatObject.ob_type -> NULL
可能是为了不增加新的链表类似的next字段，直接粗暴的使用ob_type当做链表的next字段来使用。

继续看`PyObject_MALLOC`宏，这个宏背后较为复杂，实质是先去内存池中申请，申请不到就去调用malloc申请内存，这个有点类似于`nginx`的内存池。

`PyObject_INIT`宏的作用是初始化`ob_type`与引用计数`ob_refcnt`


### float实例对象的行为
都知道对象的行为是由类决定的，在`PyFloat_Type`定义中找到描述`float`实例对象的行为的信息，截取部分如下

    &float_as_number,       /* tp_as_number */
    0,                      /* tp_as_sequence */
    0,                      /* tp_as_mapping */
    (hashfunc)float_hash,   /* tp_hash */

我们找到一个重要的定义`float_as_number`，描述了数值类型的支持的行为，因此`float`实例对象自然就支持数值类型操作，以减法操作为例，我们找到`float_sub`

    static PyObject *
    float_sub(PyObject *v, PyObject *w)
    {
        double a,b;
        // 将pyobject类型转为double
        CONVERT_TO_DOUBLE(v, a);
        CONVERT_TO_DOUBLE(w, b);
        // 此宏基本不使用
        PyFPE_START_PROTECT("subtract", return 0)
        // 执行减法操作
        a = a - b;
        // 此宏基本不使用
        PyFPE_END_PROTECT(a)
        // 重新生成float实例对象
        return PyFloat_FromDouble(a);
    }

### 销毁`float`实例对象
找到`float_dealloc`函数，直接看代码

    static void
    float_dealloc(PyFloatObject *op)
    {
        if (PyFloat_CheckExact(op)) {
            if (numfree >= PyFloat_MAXFREELIST)  {
                // 大于空闲链表最大长度，直接销毁`Float`实例对象
                PyObject_FREE(op);
                return;
            }
            numfree++;
            // 将float实例对象加入空闲链表中头部，并将free_list执行当前实例对象
            Py_TYPE(op) = (struct _typeobject *)free_list;
            free_list = op;
        }
        else
            // 若不是内置`float`实例对象，则直接销毁该对象
            Py_TYPE(op)->tp_free((PyObject *)op);
    }

我们在`Python`解释器下验证一下空闲链表缓存

    >>> a = 3.14
    >>> id(a)
    4428025040
    >>> b = 3.15
    >>> id(b)
    4428023632
    >>> del a
    >>> c = 3.16
    >>> id(c)
    4428025040

### 总结
通过对最简单的内建`float`类型的分析，我们发现所有接口的入参都用PyObject类型泛指，就导致了`Python`在各个接口处理时都需要花大量精力去处理类型问题，应该也是导致`Python`运行慢的一个重要原因。