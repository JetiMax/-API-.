import json

with open('D:\Downloads\New document 2.json', encoding='utf8') as f:
    templates = json.load(f)


def checkint(item):
    return (item, int)  # требования


def checkstr(item):
    return (item, str)  # требования


def checkbool(item):
    return (item, bool)  # требования


def checkurl(item):
    if (item, str):  # требования
        return item.startswith('http://') or item.startswith('https://')
    else:
        return False


def checkstrvalue(item, val):
    if (item, str):
        return item in val
    else:
        return False


def errorlog(item, value, string):
    Error.append({item: f'{value}, {string}'})


listofitems = {'timestamp': 'int', 'referer': 'url', 'location': 'url', 'remoteHost': 'str', 'partyId': 'str',
               'sessionId': 'str', 'pageViewId': 'str', 'eventType': 'val', 'item_id': 'str', 'item_price': 'int',
               'item_url': 'url', 'basket_price': 'str', 'detectedDuplicate': 'bool', 'detectedCorruption': 'bool',
               'firstInSession': 'bool', 'userAgentName': 'str'}


Error = []
for item in templates:
    for item in item:
        if item in listofitems:
            if listofitems[item] == 'int':
                if not checkint(item[item]):
                    errorlog(item[item], f'expected type {listofitems[item]}')
            elif listofitems[item] == 'str':
                if not checkstr(item[item]):
                    errorlog(item[item], f'expected type {listofitems[item]}')
            elif listofitems[item] == 'bool':
                if not checkbool(item[item]):
                    errorlog(item[item], f'ожидали тип {listofitems[item]}')
            elif listofitems[item] == 'url':
                if not checkurl(item[item]):
                    errorlog(item[item], f'ожидали тип {listofitems[item]}')
            elif listofitems[item] == 'val':
                if not checkstrvalue(item[item], ['itemBuyEvent', 'itemViewEvent']):
                    errorlog(item, item[item], 'ожидали значение itemBuyEvent или itemViewEvent')
            else:
                errorlog(item, item[item], 'неожиданное значение')
        else:
            errorlog(item, item[item], 'неизвестная переменная')

if Error == []:
    print('Pass')
else:
    print('Fail')
    print('Error')




#2ой вариант


import json

dict_temp = {
    'timestamp': int,
    'referer': str,
    'location': str,
    'remoteHost': str,
    'partyId': str,
    'sessionId': str,
    'pageViewId': str,
    'eventType': str,
    'item_id': str,
    'item_price': int,
    'item_url': str,
    'basket_price': str,
    'detectedDuplicate': bool,
    'detectedCorruption': bool,
    'firstInSession': bool,
    'userAgentName': str
}

lst_url_fld = ['referer', 'location', 'item_url']
lst_url_prf = ['http://', 'https://']
lst_event_type = ['itemBuyEvent', 'itemViewEvent']
temp_keys_set = set(dict_temp.keys())


# Проверка типов
def chk_type(key, val, ind):
    res = 0
    if dict_temp[key] != type(val):
        print(f'JSON[{ind}] : key "{key}" {type(val)}')
        res = 1
    return res


# Проверка url
def chk_url(key, val, ind):
    res = 0
    if type(val) == str and key in lst_url_fld:
        if not val.startswith(lst_url_prf[0]) and not val.startswith(lst_url_prf[1]):
            print(f'JSON[{ind}] : key "{key}" not url')
            res = 1
    return res


# Проверка eventType
def chk_event(key, val, ind):
    res = 0
    if type(val) == str and key == 'eventType' and val not in lst_event_type:
        print(f'JSON[{ind}] : key "{key}" invalid value')
        res = 1
    return res


with open('test.json', encoding='utf8') as f:
    json_lst = list(json.load(f))

# Проверка JSON
cnt_err = 0
for obj_ in json_lst:
    obj_ind = json_lst.index(obj_)
    obj_keys_set = set(obj_.keys())

    # Сначала проверяем поля, затем их типы, у неправильного поля нет смысла проверять тип
    if obj_keys_set != temp_keys_set:
        temp_obj_set = temp_keys_set.difference(obj_keys_set)
        obj_temp_set = obj_keys_set.difference(temp_keys_set)

        if temp_obj_set:
            print(f'JSON[{obj_ind}] : missing field {temp_obj_set}')
            cnt_err += 1
        if obj_temp_set:
            print(f'JSON[{obj_ind}] : extra field {obj_temp_set}')
            cnt_err += 1
    else:
        for key_, val_ in obj_.items():
            cnt_err += chk_type(key_, val_, obj_ind)
            cnt_err += chk_url(key_, val_, obj_ind)
            cnt_err += chk_event(key_, val_, obj_ind)

if not cnt_err:
    print('JSON check pass')
else:
    print(f'Errors : {cnt_err}')
