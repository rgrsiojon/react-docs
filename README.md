# Cấu trúc ứng dụng cho React js

## 1.Cấu trúc 1

### Cấu trúc 
```
- app 
    - data 
        - spec
        - artifacts
        - calls
        - filters
        - threads 
    - containers
    - components
    index.js
    rootReducer.js 
```
### Ưu điểm
    1. Cô lập phần miền dữ liệu và logic
    2. Các view sẽ được xây dựng dựa trên miền dữ liệu và sự phụ thuộc xác định
    3. Các component được tái sử dụng cho các view 

### Đóng gói miền dữ liệu 
    Tạo file index.js và đóng gói dữ liệu vào đó
    ```
    // data/calls/index.js
        import CallReducer from './CallReducer';
        import * as CallSelectors from './CallSelectors';
        import * as CallActions from './CallActions';
        export {
            CallReducer,
            CallSelectors,
            CallActions
        }
    // calls data 
    import { CallReducer, CallSelectors, CallActions } from '../data/calls'

    ```

### Lưu ý: Test nằm ở spec, krama

## Cấu trúc thứ 2

### Vấn đề import 

```
    - components
        - FilterSlider
            - tests
                FilterSlider-test.js
            FilterSlider.js
            FilterSlider.css
            package.json
```
#### File `package.json` sẽ đống vài trò làm rút ngắn path tới file FilterSlider.js

`package.json`
```
{
    "main": "FilterSlider.js"
}
```
```
import FilerSlider from 'root/FilterSlider'
```

### CSS trong React

- Styled components 
- EmotionJS
- Glamorous

### Các component phải có tính đơn nhiệm
### Sử dụng higher-order-component 

```
import React, { Component } from 'react';
import PropTypes from 'prop-types';
import { connect } from 'react-redux';
import { push } from 'react-router-redux';

export default function requiresAuth(WrappedComponent) {
    class AuthenticatedComponent extends Component {
        static propTypes = {
            user: PropTypes.object,
            dispatch: PropTypes.func.isRequired
        };

        componentDidMount() {
            this._checkAndRedirect();
        }

        componentDidUpdate() {
            this._checkAndRedirect();
        }

        _checkAndRedirect() {
            const { dispatch, user } = this.props;

            if (!user) {
            dispatch(push('/signin'));
            }
        }

        render() {
            return (
            <div className="authenticated">
                { this.props.user ? <WrappedComponent {...this.props} /> : null }
            </div>
            );
        }
        }

        const wrappedComponentName = WrappedComponent.displayName || WrappedComponent.name || 'Component';
        AuthenticatedComponent.displayName = `Authenticated(${wrappedComponentName})`;

        const mapStateToProps = (state) => {
        return {
            user: state.account.user
        };
    };

    return connect(mapStateToProps)(AuthenticatedComponent);
}
```

### Truyền hàm render vào component 

```
export default class Table extends Component {
  render() {
    return (
        <table>
            <thead>
                <tr>
                <th>Just a table</th>
                </tr>
            </thead>
            {this.props.children}
        </table>
    );
  }
}

```

```
import React, { Component } from "react";
 
export default class CollapsibleTableBody extends Component {
    constructor(props) {
        super(props);
        this.state = { collapsed: false };
    }

    toggleCollapse = () => {
        this.setState({ collapsed: !this.state.collapsed });
    };

    render() {
        return (
            <tbody>
                {this.props.children(this.state.collapsed, this.toggleCollapse)}
            </tbody>
        );
    }
}

//@ You’d use this component in the following way:
 
<Table>
    <CollapsibleTableBody>
        {(collapsed, toggleCollapse) => {
            if (collapsed) {
            return (
                <tr>
                    <td>
                        <button onClick={toggleCollapse}>Open</button>
                    </td>
                </tr>
            );
            } else {
            return (
                <tr>
                    <td>
                        <button onClick={toggleCollapse}>Closed</button>
                    </td>
                    <td>CollapsedContent</td>
                </tr>
            );
            }
        }}
    </CollapsibleTableBody>
</Table>
```