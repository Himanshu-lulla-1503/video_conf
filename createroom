import React from 'react';
import {useHistory} from 'react-router-dom';
import { v4 as uuidv4 } from 'uuid';
function CreateRoom() {
    let history = useHistory();
    const createroom = ()=>{
        const roomId= uuidv4();
        history.push(`/room/${roomId}`);
    }
    return (
        <button onClick={createroom}>CreateRoom</button>
    )
}

export default CreateRoom;
