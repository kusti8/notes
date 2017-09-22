React Native - Ignite Thunk
================================

Install
--------------
``ignite add redux-thunk``

Usage
-------------

.. code-block:: javascript

  // --------------Main Screen----------------------
  import MainActions, {send} from '../Redux/MainRedux'

  // --------------Main Redux------------------------
  export const send = (params) => {
    return (dispatch) => {
      console.log(params)
      dispatch(Creators.mainRequest(params))
      let i = new Izzati("http://192.168.100.113:5020/")
      return i.send({text: JSON.parse(params), response: {base64: true} }).then(out => {
        dispatch(Creators.mainSuccess(i.prefixJpg(out.base64)))
      }).catch(err => {
        dispatch(Creators.mainFailure())
        console.log(err)
      })
    }
  }